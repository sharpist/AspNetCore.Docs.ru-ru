---
<span data-ttu-id="96f94-101">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-102">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-103">'Blazor'</span></span>
- <span data-ttu-id="96f94-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-104">'Identity'</span></span>
- <span data-ttu-id="96f94-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-106">'Razor'</span></span>
- <span data-ttu-id="96f94-107">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="96f94-108">ASP.NET Core Blazor дополнительных сценариев безопасности для сборки</span><span class="sxs-lookup"><span data-stu-id="96f94-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="96f94-109">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="96f94-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="96f94-110">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="96f94-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="96f94-111">`AuthorizationMessageHandler`Службу можно использовать с `HttpClient` для присоединения маркеров доступа к исходящим запросам.</span><span class="sxs-lookup"><span data-stu-id="96f94-111">The `AuthorizationMessageHandler` service can be used with `HttpClient` to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="96f94-112">Токены получаются с помощью существующей `IAccessTokenProvider` службы.</span><span class="sxs-lookup"><span data-stu-id="96f94-112">Tokens are acquired using the existing `IAccessTokenProvider` service.</span></span> <span data-ttu-id="96f94-113">Если токен не может быть получен, `AccessTokenNotAvailableException` создается исключение.</span><span class="sxs-lookup"><span data-stu-id="96f94-113">If a token can't be acquired, an `AccessTokenNotAvailableException` is thrown.</span></span> <span data-ttu-id="96f94-114">`AccessTokenNotAvailableException`содержит `Redirect` метод, который можно использовать для перехода пользователя к поставщику удостоверений для получения нового маркера.</span><span class="sxs-lookup"><span data-stu-id="96f94-114">`AccessTokenNotAvailableException` has a `Redirect` method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="96f94-115">Для `AuthorizationMessageHandler` настройки можно использовать полномочные URL-адреса, области и URL-адрес возврата с помощью `ConfigureHandler` метода.</span><span class="sxs-lookup"><span data-stu-id="96f94-115">The `AuthorizationMessageHandler` can be configured with the authorized URLs, scopes, and return URL using the `ConfigureHandler` method.</span></span>

<span data-ttu-id="96f94-116">В следующем примере `AuthorizationMessageHandler` настраивается `HttpClient` в `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="96f94-116">In the following example, `AuthorizationMessageHandler` configures an `HttpClient` in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="96f94-117">Для удобства `BaseAddressAuthorizationMessageHandler` включается предварительно настроенный базовый адрес приложения в качестве разрешенного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="96f94-117">For convenience, a `BaseAddressAuthorizationMessageHandler` is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="96f94-118">Шаблоны сборки с поддержкой проверки подлинности Blazor теперь используют <xref:System.Net.Http.IHttpClientFactory> в проекте API сервера для настройки с помощью <xref:System.Net.Http.HttpClient> `BaseAddressAuthorizationMessageHandler` :</span><span class="sxs-lookup"><span data-stu-id="96f94-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the `BaseAddressAuthorizationMessageHandler`:</span></span>

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

<span data-ttu-id="96f94-119">При создании клиента с помощью `CreateClient` в предыдущем примере <xref:System.Net.Http.HttpClient> передаются экземпляры, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="96f94-119">Where the client is created with `CreateClient` in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="96f94-120">Настроенный <xref:System.Net.Http.HttpClient> затем используется для выполнения запросов с помощью простого `try-catch` шаблона.</span><span class="sxs-lookup"><span data-stu-id="96f94-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple `try-catch` pattern.</span></span>

<span data-ttu-id="96f94-121">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="96f94-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="96f94-122">Типизированный класс HttpClient</span><span class="sxs-lookup"><span data-stu-id="96f94-122">Typed HttpClient</span></span>

<span data-ttu-id="96f94-123">Можно определить типизированный клиент, который обрабатывает все проблемы получения HTTP и маркеров в рамках одного класса.</span><span class="sxs-lookup"><span data-stu-id="96f94-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="96f94-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="96f94-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="96f94-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96f94-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="96f94-126">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="96f94-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="96f94-127">Настройка обработчика HttpClient</span><span class="sxs-lookup"><span data-stu-id="96f94-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="96f94-128">Обработчик можно дополнительно настроить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="96f94-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="96f94-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96f94-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="96f94-130">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="96f94-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="96f94-131">Если приложение веб- Blazor сборки обычно использует безопасное значение по умолчанию <xref:System.Net.Http.HttpClient> , оно также может выполнять непроверенные или неавторизованные запросы веб-API, настроив именованную <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="96f94-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="96f94-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96f94-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="96f94-133">Приведенная выше регистрация является дополнением к существующей безопасной регистрации по умолчанию <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="96f94-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="96f94-134">Компонент создает <xref:System.Net.Http.HttpClient> из в <xref:System.Net.Http.IHttpClientFactory> для создания непроверенных или неавторизованных запросов:</span><span class="sxs-lookup"><span data-stu-id="96f94-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="96f94-135">Контроллер в API сервера `WeatherForecastNoAuthenticationController` для предыдущего примера не помечен [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибутом.</span><span class="sxs-lookup"><span data-stu-id="96f94-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="96f94-136">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="96f94-136">Request additional access tokens</span></span>

<span data-ttu-id="96f94-137">Маркеры доступа могут быть получены вручную путем вызова `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="96f94-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="96f94-138">В следующем примере дополнительные Azure Active Directory (AAD) Microsoft Graph области API необходимы приложениям для чтения данных пользователей и отправки почты.</span><span class="sxs-lookup"><span data-stu-id="96f94-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="96f94-139">После добавления разрешений Microsoft Graph API на портале Azure AAD дополнительные области настраиваются в клиентском приложении.</span><span class="sxs-lookup"><span data-stu-id="96f94-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="96f94-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96f94-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="96f94-141">`IAccessTokenProvider.RequestToken`Метод предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с заданным набором областей.</span><span class="sxs-lookup"><span data-stu-id="96f94-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="96f94-142">В Razor компоненте:</span><span class="sxs-lookup"><span data-stu-id="96f94-142">In a Razor component:</span></span>

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

<span data-ttu-id="96f94-143">`TryGetToken`возврата</span><span class="sxs-lookup"><span data-stu-id="96f94-143">`TryGetToken` returns:</span></span>

* <span data-ttu-id="96f94-144">`true`с помощью `token` для использования.</span><span class="sxs-lookup"><span data-stu-id="96f94-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="96f94-145">`false`значение, если маркер не извлекается.</span><span class="sxs-lookup"><span data-stu-id="96f94-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="96f94-146">HttpClient и HttpRequestMessage с параметрами запроса API FETCH</span><span class="sxs-lookup"><span data-stu-id="96f94-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="96f94-147">При запуске в сборке в Blazor приложении [HttpClient](xref:fundamentals/http-requests) и <xref:System.Net.Http.HttpRequestMessage> может использоваться для настройки запросов.</span><span class="sxs-lookup"><span data-stu-id="96f94-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="96f94-148">Например, можно указать метод HTTP и заголовки запроса.</span><span class="sxs-lookup"><span data-stu-id="96f94-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="96f94-149">Следующий компонент выполняет запрос к `POST` конечной точке API To Do List на сервере и отображает текст ответа:</span><span class="sxs-lookup"><span data-stu-id="96f94-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="96f94-150">Реализация `HttpClient` [виндоворворкерглобалскопе. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)в .NET-сборке использует.</span><span class="sxs-lookup"><span data-stu-id="96f94-150">.NET WebAssembly's implementation of `HttpClient` uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="96f94-151">Выборка позволяет настроить несколько [параметров для конкретного запроса](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="96f94-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="96f94-152">Параметры запроса HTTP FETCH можно настроить с помощью `HttpRequestMessage` методов расширения, приведенных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="96f94-152">HTTP fetch request options can be configured with `HttpRequestMessage` extension methods shown in the following table.</span></span>

| <span data-ttu-id="96f94-153">`HttpRequestMessage`метод расширения</span><span class="sxs-lookup"><span data-stu-id="96f94-153">`HttpRequestMessage` extension method</span></span> | <span data-ttu-id="96f94-154">Получение свойства запроса</span><span class="sxs-lookup"><span data-stu-id="96f94-154">Fetch request property</span></span> |
| ---
<span data-ttu-id="96f94-155">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-155">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-156">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-156">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-157">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-157">'Blazor'</span></span>
- <span data-ttu-id="96f94-158">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-158">'Identity'</span></span>
- <span data-ttu-id="96f94-159">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-159">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-160">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-160">'Razor'</span></span>
- <span data-ttu-id="96f94-161">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-161">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-162">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-162">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-163">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-163">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-164">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-164">'Blazor'</span></span>
- <span data-ttu-id="96f94-165">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-165">'Identity'</span></span>
- <span data-ttu-id="96f94-166">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-166">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-167">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-167">'Razor'</span></span>
- <span data-ttu-id="96f94-168">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-168">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-169">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-169">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-170">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-170">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-171">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-171">'Blazor'</span></span>
- <span data-ttu-id="96f94-172">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-172">'Identity'</span></span>
- <span data-ttu-id="96f94-173">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-173">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-174">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-174">'Razor'</span></span>
- <span data-ttu-id="96f94-175">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-175">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-176">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-176">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-177">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-177">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-178">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-178">'Blazor'</span></span>
- <span data-ttu-id="96f94-179">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-179">'Identity'</span></span>
- <span data-ttu-id="96f94-180">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-180">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-181">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-181">'Razor'</span></span>
- <span data-ttu-id="96f94-182">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-182">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-183">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-183">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-184">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-184">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-185">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-185">'Blazor'</span></span>
- <span data-ttu-id="96f94-186">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-186">'Identity'</span></span>
- <span data-ttu-id="96f94-187">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-187">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-188">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-188">'Razor'</span></span>
- <span data-ttu-id="96f94-189">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-189">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-190">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-190">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-191">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-191">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-192">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-192">'Blazor'</span></span>
- <span data-ttu-id="96f94-193">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-193">'Identity'</span></span>
- <span data-ttu-id="96f94-194">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-194">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-195">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-195">'Razor'</span></span>
- <span data-ttu-id="96f94-196">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-196">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-197">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-197">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-198">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-198">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-199">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-199">'Blazor'</span></span>
- <span data-ttu-id="96f94-200">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-200">'Identity'</span></span>
- <span data-ttu-id="96f94-201">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-201">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-202">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-202">'Razor'</span></span>
- <span data-ttu-id="96f94-203">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-203">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-204">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-204">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-205">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-205">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-206">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-206">'Blazor'</span></span>
- <span data-ttu-id="96f94-207">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-207">'Identity'</span></span>
- <span data-ttu-id="96f94-208">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-208">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-209">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-209">'Razor'</span></span>
- <span data-ttu-id="96f94-210">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-210">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-211">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-211">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-212">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-212">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-213">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-213">'Blazor'</span></span>
- <span data-ttu-id="96f94-214">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-214">'Identity'</span></span>
- <span data-ttu-id="96f94-215">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-215">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-216">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-216">'Razor'</span></span>
- <span data-ttu-id="96f94-217">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-217">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-218">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-218">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-219">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-219">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-220">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-220">'Blazor'</span></span>
- <span data-ttu-id="96f94-221">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-221">'Identity'</span></span>
- <span data-ttu-id="96f94-222">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-222">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-223">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-223">'Razor'</span></span>
- <span data-ttu-id="96f94-224">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-224">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-225">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-225">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-226">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-226">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-227">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-227">'Blazor'</span></span>
- <span data-ttu-id="96f94-228">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-228">'Identity'</span></span>
- <span data-ttu-id="96f94-229">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-229">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-230">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-230">'Razor'</span></span>
- <span data-ttu-id="96f94-231">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-231">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-232">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-232">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-233">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-233">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-234">'Blazor'</span></span>
- <span data-ttu-id="96f94-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-235">'Identity'</span></span>
- <span data-ttu-id="96f94-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-237">'Razor'</span></span>
- <span data-ttu-id="96f94-238">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-238">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-239">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-239">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-240">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-240">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-241">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-241">'Blazor'</span></span>
- <span data-ttu-id="96f94-242">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-242">'Identity'</span></span>
- <span data-ttu-id="96f94-243">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-243">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-244">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-244">'Razor'</span></span>
- <span data-ttu-id="96f94-245">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-245">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-246">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-246">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-247">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-247">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-248">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-248">'Blazor'</span></span>
- <span data-ttu-id="96f94-249">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-249">'Identity'</span></span>
- <span data-ttu-id="96f94-250">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-250">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-251">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-251">'Razor'</span></span>
- <span data-ttu-id="96f94-252">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-252">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-253">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-253">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-254">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-254">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-255">'Blazor'</span></span>
- <span data-ttu-id="96f94-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-256">'Identity'</span></span>
- <span data-ttu-id="96f94-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-258">'Razor'</span></span>
- <span data-ttu-id="96f94-259">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-259">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-260">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-260">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-261">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-261">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-262">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-262">'Blazor'</span></span>
- <span data-ttu-id="96f94-263">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-263">'Identity'</span></span>
- <span data-ttu-id="96f94-264">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-264">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-265">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-265">'Razor'</span></span>
- <span data-ttu-id="96f94-266">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-266">'SignalR' uid:</span></span> 

<span data-ttu-id="96f94-267">------------------- | Заголовок---: "ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки" author: Description: "Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.</span><span class="sxs-lookup"><span data-stu-id="96f94-267">------------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-268">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-268">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-269">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-269">'Blazor'</span></span>
- <span data-ttu-id="96f94-270">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-270">'Identity'</span></span>
- <span data-ttu-id="96f94-271">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-271">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-272">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-272">'Razor'</span></span>
- <span data-ttu-id="96f94-273">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-273">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-274">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-274">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-275">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-275">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-276">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-276">'Blazor'</span></span>
- <span data-ttu-id="96f94-277">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-277">'Identity'</span></span>
- <span data-ttu-id="96f94-278">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-278">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-279">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-279">'Razor'</span></span>
- <span data-ttu-id="96f94-280">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-280">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-281">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-281">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-282">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-282">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-283">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-283">'Blazor'</span></span>
- <span data-ttu-id="96f94-284">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-284">'Identity'</span></span>
- <span data-ttu-id="96f94-285">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-285">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-286">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-286">'Razor'</span></span>
- <span data-ttu-id="96f94-287">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-287">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-288">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-288">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-289">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-289">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-290">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-290">'Blazor'</span></span>
- <span data-ttu-id="96f94-291">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-291">'Identity'</span></span>
- <span data-ttu-id="96f94-292">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-292">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-293">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-293">'Razor'</span></span>
- <span data-ttu-id="96f94-294">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-294">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-295">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-295">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-296">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-296">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-297">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-297">'Blazor'</span></span>
- <span data-ttu-id="96f94-298">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-298">'Identity'</span></span>
- <span data-ttu-id="96f94-299">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-299">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-300">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-300">'Razor'</span></span>
- <span data-ttu-id="96f94-301">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-301">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-302">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-302">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-303">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-303">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-304">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-304">'Blazor'</span></span>
- <span data-ttu-id="96f94-305">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-305">'Identity'</span></span>
- <span data-ttu-id="96f94-306">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-306">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-307">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-307">'Razor'</span></span>
- <span data-ttu-id="96f94-308">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-308">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-309">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-309">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-310">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-310">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-311">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-311">'Blazor'</span></span>
- <span data-ttu-id="96f94-312">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-312">'Identity'</span></span>
- <span data-ttu-id="96f94-313">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-313">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-314">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-314">'Razor'</span></span>
- <span data-ttu-id="96f94-315">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-315">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-316">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-316">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-317">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-317">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-318">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-318">'Blazor'</span></span>
- <span data-ttu-id="96f94-319">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-319">'Identity'</span></span>
- <span data-ttu-id="96f94-320">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-320">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-321">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-321">'Razor'</span></span>
- <span data-ttu-id="96f94-322">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-322">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-323">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-323">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-324">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-324">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-325">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-325">'Blazor'</span></span>
- <span data-ttu-id="96f94-326">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-326">'Identity'</span></span>
- <span data-ttu-id="96f94-327">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-327">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-328">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-328">'Razor'</span></span>
- <span data-ttu-id="96f94-329">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-329">'SignalR' uid:</span></span> 

<span data-ttu-id="96f94-330">----------- | | `SetBrowserRequestCredentials`         |  [учетные данные](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`               |  [кэш](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`                |  [режим](https://developer.mozilla.org/docs/Web/API/Request/mode) | `SetBrowserRequestIntegrity` |           |  [целостность](https://developer.mozilla.org/docs/Web/API/Request/integrity) данных |</span><span class="sxs-lookup"><span data-stu-id="96f94-330">----------- | | `SetBrowserRequestCredentials`        | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) | | `SetBrowserRequestCache`              | [cache](https://developer.mozilla.org/docs/Web/API/Request/cache) | | `SetBrowserRequestMode`               | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) | | `SetBrowserRequestIntegrity`          | [integrity](https://developer.mozilla.org/docs/Web/API/Request/integrity) |</span></span>

<span data-ttu-id="96f94-331">Можно задать дополнительные параметры с помощью более универсального `SetBrowserRequestOption` метода расширения.</span><span class="sxs-lookup"><span data-stu-id="96f94-331">You can set additional options using the more generic `SetBrowserRequestOption` extension method.</span></span>
 
<span data-ttu-id="96f94-332">HTTP-ответ обычно замещается в буфер в Blazor приложении сборки, чтобы обеспечить поддержку синхронных операций чтения содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="96f94-332">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="96f94-333">Чтобы включить поддержку потоковой передачи ответов, используйте `SetBrowserResponseStreamingEnabled` метод расширения для запроса.</span><span class="sxs-lookup"><span data-stu-id="96f94-333">To enable support for response streaming, use the `SetBrowserResponseStreamingEnabled` extension method on the request.</span></span>

<span data-ttu-id="96f94-334">Чтобы включить учетные данные в запрос между источниками, используйте `SetBrowserRequestCredentials` метод расширения:</span><span class="sxs-lookup"><span data-stu-id="96f94-334">To include credentials in a cross-origin request, use the `SetBrowserRequestCredentials` extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="96f94-335">Дополнительные сведения о возможностях API-получения см. в разделе [MDN Web документация: виндоворворкерглобалскопе. fetch ():P араметерс](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="96f94-335">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="96f94-336">При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS этот `Authorization` заголовок должен быть разрешен ПОЛИТИКОЙ CORS.</span><span class="sxs-lookup"><span data-stu-id="96f94-336">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="96f94-337">Следующая политика включает в себя настройку для:</span><span class="sxs-lookup"><span data-stu-id="96f94-337">The following policy includes configuration for:</span></span>

* <span data-ttu-id="96f94-338">Источники запроса ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="96f94-338">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="96f94-339">Любой метод (глагол).</span><span class="sxs-lookup"><span data-stu-id="96f94-339">Any method (verb).</span></span>
* <span data-ttu-id="96f94-340">`Content-Type`и `Authorization` заголовки.</span><span class="sxs-lookup"><span data-stu-id="96f94-340">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="96f94-341">Чтобы разрешить пользовательский заголовок (например, `x-custom-header` ), выведите список заголовков при вызове метода <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="96f94-341">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="96f94-342">Учетные данные, которые задаются кодом JavaScript на стороне клиента ( `credentials` свойство имеет значение `include` ).</span><span class="sxs-lookup"><span data-stu-id="96f94-342">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="96f94-343">Дополнительные сведения см <xref:security/cors> . в разделе и компоненте тестера HTTP-запросов примера приложения (*Components/хттпрекуесттестер. Razor*).</span><span class="sxs-lookup"><span data-stu-id="96f94-343">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="96f94-344">Обработку ошибок запросов маркера</span><span class="sxs-lookup"><span data-stu-id="96f94-344">Handle token request errors</span></span>

<span data-ttu-id="96f94-345">Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в рамках SPA и в Identity поставщике (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.</span><span class="sxs-lookup"><span data-stu-id="96f94-345">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="96f94-346">Маркеры, которые IP-адрес выдает пользователю, обычно действительны в течение короткого периода времени, примерно один час, поэтому клиентское приложение должно регулярно получать новые токены.</span><span class="sxs-lookup"><span data-stu-id="96f94-346">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="96f94-347">В противном случае пользователь будет зарегистрирован после истечения срока действия назначенных токенов.</span><span class="sxs-lookup"><span data-stu-id="96f94-347">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="96f94-348">В большинстве случаев клиенты OIDC могут подготавливать новые маркеры, не требуя от пользователя повторной проверки подлинности благодаря состоянию аутентификации или сеансу, который хранится в IP-адресе.</span><span class="sxs-lookup"><span data-stu-id="96f94-348">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="96f94-349">В некоторых случаях клиент не может получить маркер без взаимодействия с пользователем, например, когда по какой-либо причине пользователь явно выходит из IP.</span><span class="sxs-lookup"><span data-stu-id="96f94-349">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="96f94-350">Эта ситуация возникает при посещении `https://login.microsoftonline.com` и выходе пользователя из систему. В этих сценариях приложение не знает о немедленном выходе пользователя из систему. Любой токен, который удерживает клиент, может быть недействительным.</span><span class="sxs-lookup"><span data-stu-id="96f94-350">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="96f94-351">Кроме того, клиент не может подготавливать новый маркер без взаимодействия с пользователем после истечения срока действия текущего маркера.</span><span class="sxs-lookup"><span data-stu-id="96f94-351">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="96f94-352">Эти сценарии не относятся к проверке подлинности на основе маркеров.</span><span class="sxs-lookup"><span data-stu-id="96f94-352">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="96f94-353">Они являются частью природы одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="96f94-353">They are part of the nature of SPAs.</span></span> <span data-ttu-id="96f94-354">Проверка подлинности с помощью файлов cookie также не вызывает API сервера, если удаляется файл cookie для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="96f94-354">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="96f94-355">Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="96f94-355">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="96f94-356">Чтобы создать новый маркер доступа для вызова API, пользователю может потребоваться выполнить повторную проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="96f94-356">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="96f94-357">Даже если у клиента есть токен, который кажется допустимым, вызов сервера может завершиться ошибкой, так как маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="96f94-357">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="96f94-358">Когда приложение запрашивает маркер, существует два возможных результата:</span><span class="sxs-lookup"><span data-stu-id="96f94-358">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="96f94-359">Запрос выполнен успешно, и приложение имеет действительный токен.</span><span class="sxs-lookup"><span data-stu-id="96f94-359">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="96f94-360">Запрос завершается ошибкой, и приложение должно повторно пройти проверку подлинности пользователя, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="96f94-360">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="96f94-361">При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="96f94-361">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="96f94-362">Существует несколько подходов, повышающих уровень сложности:</span><span class="sxs-lookup"><span data-stu-id="96f94-362">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="96f94-363">Сохранение текущего состояния страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="96f94-363">Store the current page state in session storage.</span></span> <span data-ttu-id="96f94-364">`OnInitializeAsync`В течение этого периода проверьте, можно ли восстановить состояние перед продолжением.</span><span class="sxs-lookup"><span data-stu-id="96f94-364">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="96f94-365">Добавьте параметр строки запроса и используйте его в качестве способа сигнализации приложению о необходимости повторного расконсервации ранее сохраненного состояния.</span><span class="sxs-lookup"><span data-stu-id="96f94-365">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="96f94-366">Добавьте параметр строки запроса с уникальным идентификатором для хранения данных в хранилище сеанса без риска конфликтов с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="96f94-366">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="96f94-367">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="96f94-367">The following example shows how to:</span></span>

* <span data-ttu-id="96f94-368">Сохраните состояние перед перенаправлением на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="96f94-368">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="96f94-369">Восстановите предыдущее состояние после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="96f94-369">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="96f94-370">Сохранить состояние приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="96f94-370">Save app state before an authentication operation</span></span>

<span data-ttu-id="96f94-371">Во время операции аутентификации существуют случаи, когда необходимо сохранить состояние приложения, прежде чем браузер перенаправится на IP-адрес.</span><span class="sxs-lookup"><span data-stu-id="96f94-371">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="96f94-372">Это может быть так, если вы используете нечто вроде контейнера состояния и хотите восстановить состояние после завершения проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="96f94-372">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="96f94-373">Можно использовать пользовательский объект состояния проверки подлинности, чтобы сохранить состояние конкретного приложения или ссылку на него, а затем восстановить это состояние после успешного завершения операции проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="96f94-373">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="96f94-374">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="96f94-374">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="96f94-375">Настройка маршрутов приложений</span><span class="sxs-lookup"><span data-stu-id="96f94-375">Customize app routes</span></span>

<span data-ttu-id="96f94-376">По умолчанию `Microsoft.AspNetCore.Components.WebAssembly.Authentication` Библиотека использует маршруты, показанные в следующей таблице, для представления различных состояний проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="96f94-376">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="96f94-377">Маршрут</span><span class="sxs-lookup"><span data-stu-id="96f94-377">Route</span></span>                            | <span data-ttu-id="96f94-378">Назначение</span><span class="sxs-lookup"><span data-stu-id="96f94-378">Purpose</span></span> |
| ---
<span data-ttu-id="96f94-379">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-379">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-380">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-380">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-381">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-381">'Blazor'</span></span>
- <span data-ttu-id="96f94-382">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-382">'Identity'</span></span>
- <span data-ttu-id="96f94-383">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-383">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-384">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-384">'Razor'</span></span>
- <span data-ttu-id="96f94-385">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-385">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-386">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-386">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-387">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-387">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-388">'Blazor'</span></span>
- <span data-ttu-id="96f94-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-389">'Identity'</span></span>
- <span data-ttu-id="96f94-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-391">'Razor'</span></span>
- <span data-ttu-id="96f94-392">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-393">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-393">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-394">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-394">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-395">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-395">'Blazor'</span></span>
- <span data-ttu-id="96f94-396">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-396">'Identity'</span></span>
- <span data-ttu-id="96f94-397">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-397">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-398">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-398">'Razor'</span></span>
- <span data-ttu-id="96f94-399">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-399">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-400">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-400">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-401">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-401">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-402">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-402">'Blazor'</span></span>
- <span data-ttu-id="96f94-403">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-403">'Identity'</span></span>
- <span data-ttu-id="96f94-404">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-404">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-405">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-405">'Razor'</span></span>
- <span data-ttu-id="96f94-406">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-406">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-407">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-407">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-408">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-408">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-409">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-409">'Blazor'</span></span>
- <span data-ttu-id="96f94-410">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-410">'Identity'</span></span>
- <span data-ttu-id="96f94-411">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-411">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-412">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-412">'Razor'</span></span>
- <span data-ttu-id="96f94-413">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-413">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-414">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-414">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-415">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-415">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-416">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-416">'Blazor'</span></span>
- <span data-ttu-id="96f94-417">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-417">'Identity'</span></span>
- <span data-ttu-id="96f94-418">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-418">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-419">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-419">'Razor'</span></span>
- <span data-ttu-id="96f94-420">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-420">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-421">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-421">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-422">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-422">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-423">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-423">'Blazor'</span></span>
- <span data-ttu-id="96f94-424">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-424">'Identity'</span></span>
- <span data-ttu-id="96f94-425">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-425">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-426">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-426">'Razor'</span></span>
- <span data-ttu-id="96f94-427">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-427">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-428">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-428">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-429">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-429">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-430">'Blazor'</span></span>
- <span data-ttu-id="96f94-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-431">'Identity'</span></span>
- <span data-ttu-id="96f94-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-433">'Razor'</span></span>
- <span data-ttu-id="96f94-434">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-435">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-435">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-436">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-436">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-437">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-437">'Blazor'</span></span>
- <span data-ttu-id="96f94-438">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-438">'Identity'</span></span>
- <span data-ttu-id="96f94-439">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-439">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-440">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-440">'Razor'</span></span>
- <span data-ttu-id="96f94-441">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-441">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-442">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-442">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-443">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-443">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-444">'Blazor'</span></span>
- <span data-ttu-id="96f94-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-445">'Identity'</span></span>
- <span data-ttu-id="96f94-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-447">'Razor'</span></span>
- <span data-ttu-id="96f94-448">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-448">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-449">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-449">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-450">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-450">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-451">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-451">'Blazor'</span></span>
- <span data-ttu-id="96f94-452">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-452">'Identity'</span></span>
- <span data-ttu-id="96f94-453">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-453">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-454">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-454">'Razor'</span></span>
- <span data-ttu-id="96f94-455">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-455">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-456">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-456">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-457">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-457">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-458">'Blazor'</span></span>
- <span data-ttu-id="96f94-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-459">'Identity'</span></span>
- <span data-ttu-id="96f94-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-461">'Razor'</span></span>
- <span data-ttu-id="96f94-462">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-462">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-463">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-463">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-464">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-464">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-465">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-465">'Blazor'</span></span>
- <span data-ttu-id="96f94-466">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-466">'Identity'</span></span>
- <span data-ttu-id="96f94-467">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-467">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-468">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-468">'Razor'</span></span>
- <span data-ttu-id="96f94-469">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-469">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-470">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-470">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-471">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-471">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-472">'Blazor'</span></span>
- <span data-ttu-id="96f94-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-473">'Identity'</span></span>
- <span data-ttu-id="96f94-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-475">'Razor'</span></span>
- <span data-ttu-id="96f94-476">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-476">'SignalR' uid:</span></span> 

<span data-ttu-id="96f94-477">---------------- | Заголовок---: "ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки" author: Description: "Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.</span><span class="sxs-lookup"><span data-stu-id="96f94-477">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-478">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-478">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-479">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-479">'Blazor'</span></span>
- <span data-ttu-id="96f94-480">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-480">'Identity'</span></span>
- <span data-ttu-id="96f94-481">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-481">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-482">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-482">'Razor'</span></span>
- <span data-ttu-id="96f94-483">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-483">'SignalR' uid:</span></span> 

<span data-ttu-id="96f94-484">---- | | `authentication/login`           | Активирует операцию входа.</span><span class="sxs-lookup"><span data-stu-id="96f94-484">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="96f94-485">| | `authentication/login-callback`  | Обрабатывает результат любой операции входа.</span><span class="sxs-lookup"><span data-stu-id="96f94-485">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="96f94-486">| | `authentication/login-failed`    | Отображает сообщения об ошибках при сбое операции входа по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="96f94-486">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="96f94-487">| | `authentication/logout`          | Активирует операцию выхода.</span><span class="sxs-lookup"><span data-stu-id="96f94-487">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="96f94-488">| | `authentication/logout-callback` | Обрабатывает результат операции выхода.</span><span class="sxs-lookup"><span data-stu-id="96f94-488">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="96f94-489">| | `authentication/logout-failed`   | Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="96f94-489">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="96f94-490">| | `authentication/logged-out`      | Указывает, что пользователь успешно выполнил выход.</span><span class="sxs-lookup"><span data-stu-id="96f94-490">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="96f94-491">| | `authentication/profile`         | Активирует операцию для изменения профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="96f94-491">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="96f94-492">| | `authentication/register`        | Активирует операцию для регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="96f94-492">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="96f94-493">Маршруты, показанные в предыдущей таблице, можно настроить с помощью `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths` .</span><span class="sxs-lookup"><span data-stu-id="96f94-493">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="96f94-494">При настройке параметров для предоставления настраиваемых маршрутов убедитесь, что у приложения есть маршрут, который обрабатывает каждый путь.</span><span class="sxs-lookup"><span data-stu-id="96f94-494">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="96f94-495">В следующем примере все пути имеют префикс `/security` .</span><span class="sxs-lookup"><span data-stu-id="96f94-495">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="96f94-496">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="96f94-496">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="96f94-497">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="96f94-497">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="96f94-498">Если требование вызывается для совершенно разных путей, задайте маршруты, как описано выше, и выполните визуализацию `RemoteAuthenticatorView` с помощью явного параметра Action:</span><span class="sxs-lookup"><span data-stu-id="96f94-498">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="96f94-499">При необходимости вы можете разбить пользовательский интерфейс на разные страницы.</span><span class="sxs-lookup"><span data-stu-id="96f94-499">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="96f94-500">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="96f94-500">Customize the authentication user interface</span></span>

<span data-ttu-id="96f94-501">`RemoteAuthenticatorView`включает набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="96f94-501">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="96f94-502">Каждое состояние можно настроить путем передачи пользовательского `RenderFragment` .</span><span class="sxs-lookup"><span data-stu-id="96f94-502">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="96f94-503">Для настройки отображаемого текста во время первоначального входа в систему может измениться `RemoteAuthenticatorView` следующим образом.</span><span class="sxs-lookup"><span data-stu-id="96f94-503">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="96f94-504">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="96f94-504">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="96f94-505">В `RemoteAuthenticatorView` имеется один фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="96f94-505">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="96f94-506">Маршрут</span><span class="sxs-lookup"><span data-stu-id="96f94-506">Route</span></span>                            | <span data-ttu-id="96f94-507">Fragment (Фрагмент)</span><span class="sxs-lookup"><span data-stu-id="96f94-507">Fragment</span></span>                |
| ---
<span data-ttu-id="96f94-508">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-508">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-509">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-509">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-510">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-510">'Blazor'</span></span>
- <span data-ttu-id="96f94-511">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-511">'Identity'</span></span>
- <span data-ttu-id="96f94-512">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-512">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-513">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-513">'Razor'</span></span>
- <span data-ttu-id="96f94-514">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-514">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-515">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-515">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-516">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-516">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-517">'Blazor'</span></span>
- <span data-ttu-id="96f94-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-518">'Identity'</span></span>
- <span data-ttu-id="96f94-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-520">'Razor'</span></span>
- <span data-ttu-id="96f94-521">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-522">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-522">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-523">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-523">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-524">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-524">'Blazor'</span></span>
- <span data-ttu-id="96f94-525">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-525">'Identity'</span></span>
- <span data-ttu-id="96f94-526">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-526">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-527">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-527">'Razor'</span></span>
- <span data-ttu-id="96f94-528">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-528">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-529">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-529">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-530">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-530">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-531">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-531">'Blazor'</span></span>
- <span data-ttu-id="96f94-532">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-532">'Identity'</span></span>
- <span data-ttu-id="96f94-533">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-533">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-534">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-534">'Razor'</span></span>
- <span data-ttu-id="96f94-535">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-535">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-536">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-536">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-537">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-537">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-538">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-538">'Blazor'</span></span>
- <span data-ttu-id="96f94-539">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-539">'Identity'</span></span>
- <span data-ttu-id="96f94-540">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-540">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-541">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-541">'Razor'</span></span>
- <span data-ttu-id="96f94-542">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-542">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-543">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-543">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-544">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-544">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-545">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-545">'Blazor'</span></span>
- <span data-ttu-id="96f94-546">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-546">'Identity'</span></span>
- <span data-ttu-id="96f94-547">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-547">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-548">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-548">'Razor'</span></span>
- <span data-ttu-id="96f94-549">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-549">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-550">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-550">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-551">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-551">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-552">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-552">'Blazor'</span></span>
- <span data-ttu-id="96f94-553">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-553">'Identity'</span></span>
- <span data-ttu-id="96f94-554">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-554">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-555">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-555">'Razor'</span></span>
- <span data-ttu-id="96f94-556">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-556">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-557">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-557">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-558">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-558">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-559">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-559">'Blazor'</span></span>
- <span data-ttu-id="96f94-560">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-560">'Identity'</span></span>
- <span data-ttu-id="96f94-561">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-561">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-562">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-562">'Razor'</span></span>
- <span data-ttu-id="96f94-563">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-563">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-564">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-564">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-565">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-565">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-566">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-566">'Blazor'</span></span>
- <span data-ttu-id="96f94-567">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-567">'Identity'</span></span>
- <span data-ttu-id="96f94-568">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-568">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-569">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-569">'Razor'</span></span>
- <span data-ttu-id="96f94-570">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-570">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-571">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-571">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-572">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-572">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-573">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-573">'Blazor'</span></span>
- <span data-ttu-id="96f94-574">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-574">'Identity'</span></span>
- <span data-ttu-id="96f94-575">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-575">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-576">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-576">'Razor'</span></span>
- <span data-ttu-id="96f94-577">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-577">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-578">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-578">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-579">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-579">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-580">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-580">'Blazor'</span></span>
- <span data-ttu-id="96f94-581">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-581">'Identity'</span></span>
- <span data-ttu-id="96f94-582">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-582">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-583">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-583">'Razor'</span></span>
- <span data-ttu-id="96f94-584">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-584">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-585">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-585">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-586">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-586">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-587">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-587">'Blazor'</span></span>
- <span data-ttu-id="96f94-588">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-588">'Identity'</span></span>
- <span data-ttu-id="96f94-589">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-589">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-590">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-590">'Razor'</span></span>
- <span data-ttu-id="96f94-591">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-591">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-592">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-592">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-593">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-593">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-594">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-594">'Blazor'</span></span>
- <span data-ttu-id="96f94-595">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-595">'Identity'</span></span>
- <span data-ttu-id="96f94-596">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-596">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-597">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-597">'Razor'</span></span>
- <span data-ttu-id="96f94-598">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-598">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-599">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-599">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-600">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-600">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-601">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-601">'Blazor'</span></span>
- <span data-ttu-id="96f94-602">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-602">'Identity'</span></span>
- <span data-ttu-id="96f94-603">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-603">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-604">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-604">'Razor'</span></span>
- <span data-ttu-id="96f94-605">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-605">'SignalR' uid:</span></span> 

<span data-ttu-id="96f94-606">---------------- | Заголовок---: "ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки" author: Description: "Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.</span><span class="sxs-lookup"><span data-stu-id="96f94-606">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-607">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-607">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-608">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-608">'Blazor'</span></span>
- <span data-ttu-id="96f94-609">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-609">'Identity'</span></span>
- <span data-ttu-id="96f94-610">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-610">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-611">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-611">'Razor'</span></span>
- <span data-ttu-id="96f94-612">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-612">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-613">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-613">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-614">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-614">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-615">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-615">'Blazor'</span></span>
- <span data-ttu-id="96f94-616">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-616">'Identity'</span></span>
- <span data-ttu-id="96f94-617">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-617">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-618">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-618">'Razor'</span></span>
- <span data-ttu-id="96f94-619">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-619">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-620">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-620">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-621">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-621">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-622">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-622">'Blazor'</span></span>
- <span data-ttu-id="96f94-623">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-623">'Identity'</span></span>
- <span data-ttu-id="96f94-624">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-624">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-625">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-625">'Razor'</span></span>
- <span data-ttu-id="96f94-626">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-626">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-627">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-627">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-628">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-628">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-629">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-629">'Blazor'</span></span>
- <span data-ttu-id="96f94-630">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-630">'Identity'</span></span>
- <span data-ttu-id="96f94-631">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-631">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-632">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-632">'Razor'</span></span>
- <span data-ttu-id="96f94-633">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-633">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-634">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-634">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-635">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-635">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-636">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-636">'Blazor'</span></span>
- <span data-ttu-id="96f94-637">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-637">'Identity'</span></span>
- <span data-ttu-id="96f94-638">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-638">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-639">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-639">'Razor'</span></span>
- <span data-ttu-id="96f94-640">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-640">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-641">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-641">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-642">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-642">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-643">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-643">'Blazor'</span></span>
- <span data-ttu-id="96f94-644">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-644">'Identity'</span></span>
- <span data-ttu-id="96f94-645">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-645">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-646">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-646">'Razor'</span></span>
- <span data-ttu-id="96f94-647">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-647">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-648">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-648">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-649">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-649">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-650">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-650">'Blazor'</span></span>
- <span data-ttu-id="96f94-651">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-651">'Identity'</span></span>
- <span data-ttu-id="96f94-652">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-652">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-653">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-653">'Razor'</span></span>
- <span data-ttu-id="96f94-654">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-654">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-655">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-655">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-656">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-656">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-657">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-657">'Blazor'</span></span>
- <span data-ttu-id="96f94-658">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-658">'Identity'</span></span>
- <span data-ttu-id="96f94-659">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-659">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-660">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-660">'Razor'</span></span>
- <span data-ttu-id="96f94-661">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-661">'SignalR' uid:</span></span> 

-
<span data-ttu-id="96f94-662">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="96f94-662">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="96f94-663">Моникерранже: MS. author: MS. Custom: MS. Дата: нет-Loc:</span><span class="sxs-lookup"><span data-stu-id="96f94-663">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="96f94-664">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="96f94-664">'Blazor'</span></span>
- <span data-ttu-id="96f94-665">'Identity'</span><span class="sxs-lookup"><span data-stu-id="96f94-665">'Identity'</span></span>
- <span data-ttu-id="96f94-666">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="96f94-666">'Let's Encrypt'</span></span>
- <span data-ttu-id="96f94-667">'Razor'</span><span class="sxs-lookup"><span data-stu-id="96f94-667">'Razor'</span></span>
- <span data-ttu-id="96f94-668">' SignalR ' UID:</span><span class="sxs-lookup"><span data-stu-id="96f94-668">'SignalR' uid:</span></span> 

<span data-ttu-id="96f94-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="96f94-669">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="96f94-670">Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="96f94-670">Customize the user</span></span>

<span data-ttu-id="96f94-671">Пользователи, привязанные к приложению, могут быть настроены.</span><span class="sxs-lookup"><span data-stu-id="96f94-671">Users bound to the app can be customized.</span></span> <span data-ttu-id="96f94-672">В следующем примере все пользователи, прошедшие проверку подлинности, получают `amr` утверждение для каждого из методов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="96f94-672">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="96f94-673">Создайте класс, расширяющий `RemoteUserAccount` класс:</span><span class="sxs-lookup"><span data-stu-id="96f94-673">Create a class that extends the `RemoteUserAccount` class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="96f94-674">Создайте фабрику, которая расширяет `AccountClaimsPrincipalFactory<TAccount>` :</span><span class="sxs-lookup"><span data-stu-id="96f94-674">Create a factory that extends `AccountClaimsPrincipalFactory<TAccount>`:</span></span>

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

<span data-ttu-id="96f94-675">Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="96f94-675">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="96f94-676">Любая из следующих регистраций допустима:</span><span class="sxs-lookup"><span data-stu-id="96f94-676">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="96f94-677">`AddOidcAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="96f94-677">`AddOidcAuthentication`:</span></span>

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

* <span data-ttu-id="96f94-678">`AddMsalAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="96f94-678">`AddMsalAuthentication`:</span></span>

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
  
* <span data-ttu-id="96f94-679">`AddApiAuthorization`:</span><span class="sxs-lookup"><span data-stu-id="96f94-679">`AddApiAuthorization`:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="96f94-680">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="96f94-680">Support prerendering with authentication</span></span>

<span data-ttu-id="96f94-681">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="96f94-681">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="96f94-682">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="96f94-682">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="96f94-683">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="96f94-683">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="96f94-684">В классе `Program` клиентского приложения (*Program.cs*) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="96f94-684">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="96f94-685">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="96f94-685">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="96f94-686">В методе `Startup.Configure` серверного приложения замените `endpoints.MapFallbackToFile("index.html")` на `endpoints.MapFallbackToPage("/_Host")`:</span><span class="sxs-lookup"><span data-stu-id="96f94-686">In the Server app's `Startup.Configure` method, replace `endpoints.MapFallbackToFile("index.html")` with `endpoints.MapFallbackToPage("/_Host")`:</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="96f94-687">В серверном приложении создайте папку *Pages*, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="96f94-687">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="96f94-688">В папке *Pages* серверного приложения создайте страницу *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="96f94-688">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="96f94-689">Вставьте содержимое из файла *wwwroot/index.html* клиентского приложения в файл *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="96f94-689">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="96f94-690">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="96f94-690">Update the file's contents:</span></span>

* <span data-ttu-id="96f94-691">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="96f94-691">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="96f94-692">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="96f94-692">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="96f94-693">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="96f94-693">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="96f94-694">При проверке подлинности и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="96f94-694">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="96f94-695">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="96f94-695">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="96f94-696">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="96f94-696">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="96f94-697">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="96f94-697">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="96f94-698">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="96f94-698">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="96f94-699">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="96f94-699">In this scenario:</span></span>

* <span data-ttu-id="96f94-700">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="96f94-700">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="96f94-701">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="96f94-701">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="96f94-702">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="96f94-702">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="96f94-703">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="96f94-703">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="96f94-704">Настройка Identity со сторонним поставщиком входа.</span><span class="sxs-lookup"><span data-stu-id="96f94-704">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="96f94-705">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="96f94-705">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="96f94-706">При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="96f94-706">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="96f94-707">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="96f94-707">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="96f94-708">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="96f94-708">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="96f94-709">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="96f94-709">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="96f94-710">В нем используйте сторонний маркер доступа, чтобы вызывать сторонние ресурсы API непосредственно из Identity клиента.</span><span class="sxs-lookup"><span data-stu-id="96f94-710">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="96f94-711">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="96f94-711">We don't recommend this approach.</span></span> <span data-ttu-id="96f94-712">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="96f94-712">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="96f94-713">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="96f94-713">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="96f94-714">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="96f94-714">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="96f94-715">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="96f94-715">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="96f94-716">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="96f94-716">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="96f94-717">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="96f94-717">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="96f94-718">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="96f94-718">Make an API call from the client to the server API.</span></span> <span data-ttu-id="96f94-719">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="96f94-719">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="96f94-720">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="96f94-720">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="96f94-721">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="96f94-721">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="96f94-722">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="96f94-722">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="96f94-723">Использование конечных точек Open ID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="96f94-723">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="96f94-724">Библиотека проверки подлинности и Blazor Шаблоны используют конечные точки Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="96f94-724">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="96f94-725">Чтобы использовать конечную точку версии 2.0, настройте параметр носителя JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="96f94-725">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="96f94-726">В следующем примере AAD настраивается для версии 2.0 путем добавления `v2.0` сегмента к `Authority` свойству:</span><span class="sxs-lookup"><span data-stu-id="96f94-726">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the `Authority` property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="96f94-727">Кроме того, параметр можно задать в файле параметров приложения (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="96f94-727">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="96f94-728">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например с поставщиками, не являющимися владельцами AAD, задайте `Authority` свойство напрямую.</span><span class="sxs-lookup"><span data-stu-id="96f94-728">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the `Authority` property directly.</span></span> <span data-ttu-id="96f94-729">Либо установите свойство в `JwtBearerOptions` или в файле параметров приложения с помощью `Authority` ключа.</span><span class="sxs-lookup"><span data-stu-id="96f94-729">Either set the property in `JwtBearerOptions` or in the app settings file with the `Authority` key.</span></span>

<span data-ttu-id="96f94-730">Список утверждений в токене идентификатора изменяется для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="96f94-730">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="96f94-731">Дополнительные сведения см. [в статье Почему обновление платформы Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="96f94-731">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
