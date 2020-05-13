---
title: ASP.NET Core Blazor дополнительных сценариев безопасности для сборки
author: guardrex
description: Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: d460f65e996f1f77136a426b03d6eb548d9e309e
ms.sourcegitcommit: 1250c90c8d87c2513532be5683640b65bfdf9ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83153477"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="64123-103">ASP.NET Core Блазор дополнительные сценарии безопасности для сборки</span><span class="sxs-lookup"><span data-stu-id="64123-103">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="64123-104">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="64123-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="64123-105">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="64123-105">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="64123-106">`AuthorizationMessageHandler`Службу можно использовать с `HttpClient` для присоединения маркеров доступа к исходящим запросам.</span><span class="sxs-lookup"><span data-stu-id="64123-106">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="64123-107">Токены получаются с помощью существующей `IAccessTokenProvider` службы.</span><span class="sxs-lookup"><span data-stu-id="64123-107">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="64123-108">Если токен не может быть получен, `AccessTokenNotAvailableException` создается исключение.</span><span class="sxs-lookup"><span data-stu-id="64123-108">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="64123-109">`AccessTokenNotAvailableException`содержит `Redirect` метод, который можно использовать для перехода пользователя к поставщику удостоверений для получения нового маркера.</span><span class="sxs-lookup"><span data-stu-id="64123-109">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="64123-110">Для `AuthorizationMessageHandler` настройки можно использовать полномочные URL-адреса, области и URL-адрес возврата с помощью `ConfigureHandler` метода.</span><span class="sxs-lookup"><span data-stu-id="64123-110">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="64123-111">В следующем примере `AuthorizationMessageHandler` настраивается `HttpClient` в `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="64123-111">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="64123-112">Для удобства `BaseAddressAuthorizationMessageHandler` включается предварительно настроенный базовый адрес приложения в качестве разрешенного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="64123-112">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="64123-113">Шаблоны Блазор, поддерживающие проверку подлинности, теперь используют <xref:System.Net.Http.IHttpClientFactory> в проекте API сервера для настройки <xref:System.Net.Http.HttpClient> с помощью `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="64123-113">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient("BlazorWithIdentity.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("BlazorWithIdentity.ServerAPI"));
```

<span data-ttu-id="64123-114">При создании клиента с помощью `CreateClient` в предыдущем примере <xref:System.Net.Http.HttpClient> передаются экземпляры, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="64123-114">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="64123-115">Настроенный <xref:System.Net.Http.HttpClient> затем используется для выполнения запросов с помощью простого `try-catch` шаблона.</span><span class="sxs-lookup"><span data-stu-id="64123-115">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="64123-116">`FetchData`Component (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="64123-116">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```csharp
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject HttpClient Client

...

protected override async Task OnInitializedAsync()
{
    try
    {
        forecasts = 
            await Client.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
    }
    catch (AccessTokenNotAvailableException exception)
    {
        exception.Redirect();
    }
}
```

## <a name="typed-httpclient"></a><span data-ttu-id="64123-117">Типизированный HttpClient</span><span class="sxs-lookup"><span data-stu-id="64123-117">Typed HttpClient</span></span>

<span data-ttu-id="64123-118">Можно определить типизированный клиент, который обрабатывает все проблемы получения HTTP и маркеров в рамках одного класса.</span><span class="sxs-lookup"><span data-stu-id="64123-118">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="64123-119">*WeatherForecastClient.CS*:</span><span class="sxs-lookup"><span data-stu-id="64123-119">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="64123-120">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="64123-120">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="64123-121">`FetchData`Component (*pages/FetchData. Razor*):</span><span class="sxs-lookup"><span data-stu-id="64123-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="64123-122">Настройка обработчика HttpClient</span><span class="sxs-lookup"><span data-stu-id="64123-122">Configure the HttpClient handler</span></span>

<span data-ttu-id="64123-123">Обработчик можно дополнительно настроить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="64123-123">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="64123-124">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="64123-124">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="64123-125">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="64123-125">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="64123-126">Если в приложении Блазор веб-сборки обычно используется безопасное значение по умолчанию <xref:System.Net.Http.HttpClient> , приложение также может выполнять неавторизованные или несанкционированные запросы к Web API, настроив именованную <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="64123-126">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="64123-127">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="64123-127">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="64123-128">Приведенная выше регистрация является дополнением к существующей безопасной регистрации по умолчанию <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="64123-128">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="64123-129">Компонент создает <xref:System.Net.Http.HttpClient> из в <xref:System.Net.Http.IHttpClientFactory> для создания непроверенных или неавторизованных запросов:</span><span class="sxs-lookup"><span data-stu-id="64123-129">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="64123-130">Контроллер в API сервера `WeatherForecastNoAuthenticationController` для предыдущего примера не помечен [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибутом.</span><span class="sxs-lookup"><span data-stu-id="64123-130">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="64123-131">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="64123-131">Request additional access tokens</span></span>

<span data-ttu-id="64123-132">Маркеры доступа могут быть получены вручную путем вызова `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="64123-132">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="64123-133">В следующем примере дополнительные Azure Active Directory (AAD) Microsoft Graph области API необходимы приложениям для чтения данных пользователей и отправки почты.</span><span class="sxs-lookup"><span data-stu-id="64123-133">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="64123-134">После добавления разрешений Microsoft Graph API на портале Azure AAD дополнительные области настраиваются в клиентском приложении.</span><span class="sxs-lookup"><span data-stu-id="64123-134">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="64123-135">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="64123-135">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="64123-136">`IAccessTokenProvider.RequestToken`Метод предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с заданным набором областей.</span><span class="sxs-lookup"><span data-stu-id="64123-136">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="64123-137">В компоненте Razor:</span><span class="sxs-lookup"><span data-stu-id="64123-137">In a Razor component:</span></span>

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

<span data-ttu-id="64123-138">`TryGetToken`возврата</span><span class="sxs-lookup"><span data-stu-id="64123-138">`TryGetToken` returns:</span></span>

* <span data-ttu-id="64123-139">`true`с помощью `token` для использования.</span><span class="sxs-lookup"><span data-stu-id="64123-139">`true` with the `token` for use.</span></span>
* <span data-ttu-id="64123-140">`false`значение, если маркер не извлекается.</span><span class="sxs-lookup"><span data-stu-id="64123-140">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="64123-141">HttpClient и HttpRequestMessage с параметрами запроса API FETCH</span><span class="sxs-lookup"><span data-stu-id="64123-141">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="64123-142">При выполнении в сборке Блазор в приложении [HttpClient](xref:fundamentals/http-requests) и <xref:System.Net.Http.HttpRequestMessage> может использоваться для настройки запросов.</span><span class="sxs-lookup"><span data-stu-id="64123-142">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="64123-143">Например, можно указать метод HTTP и заголовки запроса.</span><span class="sxs-lookup"><span data-stu-id="64123-143">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="64123-144">Следующий компонент выполняет запрос к `POST` конечной точке API To Do List на сервере и отображает текст ответа:</span><span class="sxs-lookup"><span data-stu-id="64123-144">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="64123-145">Реализация `HttpClient` [виндоворворкерглобалскопе. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)в .NET-сборке использует.</span><span class="sxs-lookup"><span data-stu-id="64123-145">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="64123-146">Выборка позволяет настроить несколько [параметров для конкретного запроса](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="64123-146">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="64123-147">Параметры запроса HTTP FETCH можно настроить с помощью `HttpRequestMessage` методов расширения, приведенных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="64123-147">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="64123-148">`HttpRequestMessage`метод расширения</span><span class="sxs-lookup"><span data-stu-id="64123-148">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="64123-149">Получение свойства запроса</span><span class="sxs-lookup"><span data-stu-id="64123-149">Fetch request property</span></span> |
| ------------------------------------- | ---------------------- |
| `SetBrowserRequestCredentials`        | [<span data-ttu-id="64123-150">информации</span><span class="sxs-lookup"><span data-stu-id="64123-150">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| `SetBrowserRequestCache`              | [<span data-ttu-id="64123-151">Мбайта</span><span class="sxs-lookup"><span data-stu-id="64123-151">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| `SetBrowserRequestMode`               | [<span data-ttu-id="64123-152">mode</span><span class="sxs-lookup"><span data-stu-id="64123-152">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| `SetBrowserRequestIntegrity`          | [<span data-ttu-id="64123-153">контроля</span><span class="sxs-lookup"><span data-stu-id="64123-153">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="64123-154">Можно задать дополнительные параметры с помощью более универсального `SetBrowserRequestOption` метода расширения.</span><span class="sxs-lookup"><span data-stu-id="64123-154">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="64123-155">HTTP-ответ обычно замещается в буфер приложения Блазор, чтобы обеспечить поддержку синхронных операций чтения содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="64123-155">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="64123-156">Чтобы включить поддержку потоковой передачи ответов, используйте `SetBrowserResponseStreamingEnabled` метод расширения для запроса.</span><span class="sxs-lookup"><span data-stu-id="64123-156">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="64123-157">Чтобы включить учетные данные в запрос между источниками, используйте `SetBrowserRequestCredentials` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="64123-157">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="64123-158">Дополнительные сведения о возможностях API-получения см. в разделе [MDN Web документация: виндоворворкерглобалскопе. fetch ():P араметерс](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="64123-158">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="64123-159">При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS этот `Authorization` заголовок должен быть разрешен ПОЛИТИКОЙ CORS.</span><span class="sxs-lookup"><span data-stu-id="64123-159">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="64123-160">Следующая политика включает в себя настройку для:</span><span class="sxs-lookup"><span data-stu-id="64123-160">The following policy includes configuration for:</span></span>

* <span data-ttu-id="64123-161">Источники запроса ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="64123-161">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="64123-162">Любой метод (глагол).</span><span class="sxs-lookup"><span data-stu-id="64123-162">Any method (verb).</span></span>
* <span data-ttu-id="64123-163">`Content-Type`и `Authorization` заголовки.</span><span class="sxs-lookup"><span data-stu-id="64123-163">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="64123-164">Чтобы разрешить пользовательский заголовок (например, `x-custom-header` ), выведите список заголовков при вызове метода <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="64123-164">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="64123-165">Учетные данные, которые задаются кодом JavaScript на стороне клиента ( `credentials` свойство имеет значение `include` ).</span><span class="sxs-lookup"><span data-stu-id="64123-165">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="64123-166">Дополнительные сведения см <xref:security/cors> . в разделе и компоненте тестера HTTP-запросов примера приложения (*Components/хттпрекуесттестер. Razor*).</span><span class="sxs-lookup"><span data-stu-id="64123-166">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="64123-167">Обработку ошибок запросов маркера</span><span class="sxs-lookup"><span data-stu-id="64123-167">Handle token request errors</span></span>

<span data-ttu-id="64123-168">Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в рамках SPA и в поставщике удостоверений (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.</span><span class="sxs-lookup"><span data-stu-id="64123-168">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="64123-169">Маркеры, которые IP-адрес выдает пользователю, обычно действительны в течение короткого периода времени, примерно один час, поэтому клиентское приложение должно регулярно получать новые токены.</span><span class="sxs-lookup"><span data-stu-id="64123-169">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="64123-170">В противном случае пользователь будет зарегистрирован после истечения срока действия назначенных токенов.</span><span class="sxs-lookup"><span data-stu-id="64123-170">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="64123-171">В большинстве случаев клиенты OIDC могут подготавливать новые маркеры, не требуя от пользователя повторной проверки подлинности благодаря состоянию аутентификации или сеансу, который хранится в IP-адресе.</span><span class="sxs-lookup"><span data-stu-id="64123-171">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="64123-172">В некоторых случаях клиент не может получить маркер без взаимодействия с пользователем, например, когда по какой-либо причине пользователь явно выходит из IP.</span><span class="sxs-lookup"><span data-stu-id="64123-172">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="64123-173">Эта ситуация возникает при посещении `https://login.microsoftonline.com` и выходе пользователя из систему. В этих сценариях приложение не знает о немедленном выходе пользователя из систему. Любой токен, который удерживает клиент, может быть недействительным.</span><span class="sxs-lookup"><span data-stu-id="64123-173">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="64123-174">Кроме того, клиент не может подготавливать новый маркер без взаимодействия с пользователем после истечения срока действия текущего маркера.</span><span class="sxs-lookup"><span data-stu-id="64123-174">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="64123-175">Эти сценарии не относятся к проверке подлинности на основе маркеров.</span><span class="sxs-lookup"><span data-stu-id="64123-175">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="64123-176">Они являются частью природы одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="64123-176">They are part of the nature of SPAs.</span></span> <span data-ttu-id="64123-177">Проверка подлинности с помощью файлов cookie также не вызывает API сервера, если удаляется файл cookie для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="64123-177">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="64123-178">Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="64123-178">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="64123-179">Чтобы создать новый маркер доступа для вызова API, пользователю может потребоваться выполнить повторную проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="64123-179">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="64123-180">Даже если у клиента есть токен, который кажется допустимым, вызов сервера может завершиться ошибкой, так как маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="64123-180">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="64123-181">Когда приложение запрашивает маркер, существует два возможных результата:</span><span class="sxs-lookup"><span data-stu-id="64123-181">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="64123-182">Запрос выполнен успешно, и приложение имеет действительный токен.</span><span class="sxs-lookup"><span data-stu-id="64123-182">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="64123-183">Запрос завершается ошибкой, и приложение должно повторно пройти проверку подлинности пользователя, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="64123-183">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="64123-184">При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="64123-184">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="64123-185">Существует несколько подходов, повышающих уровень сложности:</span><span class="sxs-lookup"><span data-stu-id="64123-185">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="64123-186">Сохранение текущего состояния страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="64123-186">Store the current page state in session storage.</span></span> <span data-ttu-id="64123-187">`OnInitializeAsync`В течение этого периода проверьте, можно ли восстановить состояние перед продолжением.</span><span class="sxs-lookup"><span data-stu-id="64123-187">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="64123-188">Добавьте параметр строки запроса и используйте его в качестве способа сигнализации приложению о необходимости повторного расконсервации ранее сохраненного состояния.</span><span class="sxs-lookup"><span data-stu-id="64123-188">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="64123-189">Добавьте параметр строки запроса с уникальным идентификатором для хранения данных в хранилище сеанса без риска конфликтов с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="64123-189">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="64123-190">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="64123-190">The following example shows how to:</span></span>

* <span data-ttu-id="64123-191">Сохраните состояние перед перенаправлением на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="64123-191">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="64123-192">Восстановите предыдущее состояние после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="64123-192">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="64123-193">Сохранить состояние приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="64123-193">Save app state before an authentication operation</span></span>

<span data-ttu-id="64123-194">Во время операции аутентификации существуют случаи, когда необходимо сохранить состояние приложения, прежде чем браузер перенаправится на IP-адрес.</span><span class="sxs-lookup"><span data-stu-id="64123-194">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="64123-195">Это может быть так, если вы используете нечто вроде контейнера состояния и хотите восстановить состояние после завершения проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="64123-195">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="64123-196">Можно использовать пользовательский объект состояния проверки подлинности, чтобы сохранить состояние конкретного приложения или ссылку на него, а затем восстановить это состояние после успешного завершения операции проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="64123-196">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="64123-197">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="64123-197">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="64123-198">Настройка маршрутов приложений</span><span class="sxs-lookup"><span data-stu-id="64123-198">Customize app routes</span></span>

<span data-ttu-id="64123-199">По умолчанию `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Библиотека использует маршруты, показанные в следующей таблице, для представления различных состояний проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="64123-199">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="64123-200">Маршрут</span><span class="sxs-lookup"><span data-stu-id="64123-200">Route</span></span>                            | <span data-ttu-id="64123-201">Назначение</span><span class="sxs-lookup"><span data-stu-id="64123-201">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="64123-202">Активирует операцию входа.</span><span class="sxs-lookup"><span data-stu-id="64123-202">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="64123-203">Обрабатывает результат любой операции входа.</span><span class="sxs-lookup"><span data-stu-id="64123-203">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="64123-204">Отображает сообщения об ошибках при сбое операции входа по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="64123-204">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="64123-205">Активирует операцию выхода.</span><span class="sxs-lookup"><span data-stu-id="64123-205">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="64123-206">Обрабатывает результат операции выхода.</span><span class="sxs-lookup"><span data-stu-id="64123-206">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="64123-207">Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="64123-207">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="64123-208">Указывает, что пользователь успешно выполнил выход.</span><span class="sxs-lookup"><span data-stu-id="64123-208">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="64123-209">Активирует операцию для изменения профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="64123-209">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="64123-210">Активирует операцию для регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="64123-210">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="64123-211">Маршруты, показанные в предыдущей таблице, можно настроить с помощью `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="64123-211">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="64123-212">При настройке параметров для предоставления настраиваемых маршрутов убедитесь, что у приложения есть маршрут, который обрабатывает каждый путь.</span><span class="sxs-lookup"><span data-stu-id="64123-212">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="64123-213">В следующем примере все пути имеют префикс `/security` .</span><span class="sxs-lookup"><span data-stu-id="64123-213">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="64123-214">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="64123-214">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="64123-215">`Program.Main`(*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="64123-215">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="64123-216">Если требование вызывается для совершенно разных путей, задайте маршруты, как описано выше, и выполните визуализацию `RemoteAuthenticatorView` с помощью явного параметра Action:</span><span class="sxs-lookup"><span data-stu-id="64123-216">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="64123-217">При необходимости вы можете разбить пользовательский интерфейс на разные страницы.</span><span class="sxs-lookup"><span data-stu-id="64123-217">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="64123-218">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="64123-218">Customize the authentication user interface</span></span>

<span data-ttu-id="64123-219">`RemoteAuthenticatorView`включает набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="64123-219">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="64123-220">Каждое состояние можно настроить путем передачи пользовательского `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="64123-220">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="64123-221">Для настройки отображаемого текста во время первоначального входа в систему может измениться `RemoteAuthenticatorView` следующим образом.</span><span class="sxs-lookup"><span data-stu-id="64123-221">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="64123-222">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="64123-222">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="64123-223">В `RemoteAuthenticatorView` имеется один фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="64123-223">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="64123-224">Маршрут</span><span class="sxs-lookup"><span data-stu-id="64123-224">Route</span></span>                            | <span data-ttu-id="64123-225">Fragment (Фрагмент)</span><span class="sxs-lookup"><span data-stu-id="64123-225">Fragment</span></span>                |
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

## <a name="customize-the-user"></a><span data-ttu-id="64123-226">Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="64123-226">Customize the user</span></span>

<span data-ttu-id="64123-227">Пользователи, привязанные к приложению, могут быть настроены.</span><span class="sxs-lookup"><span data-stu-id="64123-227">Users bound to the app can be customized.</span></span> <span data-ttu-id="64123-228">В следующем примере все пользователи, прошедшие проверку подлинности, получают `amr` утверждение для каждого из методов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="64123-228">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="64123-229">Создайте класс, расширяющий `RemoteUserAccount` класс:</span><span class="sxs-lookup"><span data-stu-id="64123-229">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="64123-230">Создайте фабрику, которая расширяет `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="64123-230">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="64123-231">Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="64123-231">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="64123-232">Любая из следующих регистраций допустима:</span><span class="sxs-lookup"><span data-stu-id="64123-232">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="64123-233">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="64123-233">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="64123-234">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="64123-234">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="64123-235">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="64123-235">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="64123-236">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="64123-236">Support prerendering with authentication</span></span>

<span data-ttu-id="64123-237">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="64123-237">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="64123-238">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="64123-238">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="64123-239">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="64123-239">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="64123-240">В классе `Program` клиентского приложения (*Program.cs*) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="64123-240">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="64123-241">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="64123-241">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="64123-242">В методе `Startup.Configure` серверного приложения замените `endpoints.MapFallbackToFile("index.html")` на `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="64123-242">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="64123-243">В серверном приложении создайте папку *Pages*, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="64123-243">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="64123-244">В папке *Pages* серверного приложения создайте страницу *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="64123-244">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="64123-245">Вставьте содержимое из файла *wwwroot/index.html* клиентского приложения в файл *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="64123-245">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="64123-246">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="64123-246">Update the file's contents:</span></span>

* <span data-ttu-id="64123-247">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="64123-247">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="64123-248">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="64123-248">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="64123-249">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="64123-249">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="64123-250">При проверке подлинности и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="64123-250">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="64123-251">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="64123-251">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="64123-252">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="64123-252">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="64123-253">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="64123-253">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="64123-254">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="64123-254">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="64123-255">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="64123-255">In this scenario:</span></span>

* <span data-ttu-id="64123-256">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="64123-256">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="64123-257">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="64123-257">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="64123-258">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="64123-258">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="64123-259">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="64123-259">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="64123-260">Настройка Identity со сторонним поставщиком входа.</span><span class="sxs-lookup"><span data-stu-id="64123-260">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="64123-261">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="64123-261">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="64123-262">При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="64123-262">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="64123-263">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="64123-263">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="64123-264">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="64123-264">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="64123-265">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="64123-265">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="64123-266">В нем используйте сторонний маркер доступа, чтобы вызывать сторонние ресурсы API непосредственно из Identity клиента.</span><span class="sxs-lookup"><span data-stu-id="64123-266">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="64123-267">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="64123-267">We don't recommend this approach.</span></span> <span data-ttu-id="64123-268">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="64123-268">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="64123-269">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="64123-269">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="64123-270">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="64123-270">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="64123-271">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="64123-271">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="64123-272">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="64123-272">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="64123-273">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="64123-273">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="64123-274">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="64123-274">Make an API call from the client to the server API.</span></span> <span data-ttu-id="64123-275">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="64123-275">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="64123-276">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="64123-276">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="64123-277">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="64123-277">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="64123-278">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="64123-278">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>
