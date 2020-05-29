---
<span data-ttu-id="dc561-101">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-101">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-103">'Blazor'</span></span>
- <span data-ttu-id="dc561-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-104">'Identity'</span></span>
- <span data-ttu-id="dc561-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-106">'Razor'</span></span>
- <span data-ttu-id="dc561-107">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-107">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="dc561-108">ASP.NET Core Blazor дополнительных сценариев безопасности для сборки</span><span class="sxs-lookup"><span data-stu-id="dc561-108">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="dc561-109">Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)</span><span class="sxs-lookup"><span data-stu-id="dc561-109">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

## <a name="attach-tokens-to-outgoing-requests"></a><span data-ttu-id="dc561-110">Присоединение маркеров к исходящим запросам</span><span class="sxs-lookup"><span data-stu-id="dc561-110">Attach tokens to outgoing requests</span></span>

<span data-ttu-id="dc561-111"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Службу можно использовать с <xref:System.Net.Http.HttpClient> для присоединения маркеров доступа к исходящим запросам.</span><span class="sxs-lookup"><span data-stu-id="dc561-111">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> service can be used with <xref:System.Net.Http.HttpClient> to attach access tokens to outgoing requests.</span></span> <span data-ttu-id="dc561-112">Токены получаются с помощью существующей <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> службы.</span><span class="sxs-lookup"><span data-stu-id="dc561-112">Tokens are acquired using the existing <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> service.</span></span> <span data-ttu-id="dc561-113">Если токен не может быть получен, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> создается исключение.</span><span class="sxs-lookup"><span data-stu-id="dc561-113">If a token can't be acquired, an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> is thrown.</span></span> <span data-ttu-id="dc561-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>содержит <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> метод, который можно использовать для перехода пользователя к поставщику удостоверений для получения нового маркера.</span><span class="sxs-lookup"><span data-stu-id="dc561-114"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> has a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> method that can be used to navigate the user to the identity provider to acquire a new token.</span></span> <span data-ttu-id="dc561-115">Для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настройки можно использовать полномочные URL-адреса, области и URL-адрес возврата с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> метода.</span><span class="sxs-lookup"><span data-stu-id="dc561-115">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> can be configured with the authorized URLs, scopes, and return URL using the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> method.</span></span>

<span data-ttu-id="dc561-116">В следующем примере <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настраивается <xref:System.Net.Http.HttpClient> в `Program.Main` (*Program.CS*):</span><span class="sxs-lookup"><span data-stu-id="dc561-116">In the following example, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> configures an <xref:System.Net.Http.HttpClient> in `Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="dc561-117">Для удобства <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> включается предварительно настроенный базовый адрес приложения в качестве разрешенного URL-адреса.</span><span class="sxs-lookup"><span data-stu-id="dc561-117">For convenience, a <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> is included that's preconfigured with the app base address as an authorized URL.</span></span> <span data-ttu-id="dc561-118">Шаблоны сборки с поддержкой проверки подлинности Blazor теперь используют <xref:System.Net.Http.IHttpClientFactory> в проекте API сервера для настройки с помощью <xref:System.Net.Http.HttpClient> <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :</span><span class="sxs-lookup"><span data-stu-id="dc561-118">The authentication-enabled Blazor WebAssembly templates now use <xref:System.Net.Http.IHttpClientFactory> in the Server API project to set up an <xref:System.Net.Http.HttpClient> with the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:</span></span>

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

<span data-ttu-id="dc561-119">При создании клиента с помощью <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> в предыдущем примере <xref:System.Net.Http.HttpClient> передаются экземпляры, которые включают маркеры доступа при выполнении запросов к серверному проекту.</span><span class="sxs-lookup"><span data-stu-id="dc561-119">Where the client is created with <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> in the preceding example, the <xref:System.Net.Http.HttpClient> is supplied instances that include access tokens when making requests to the server project.</span></span>

<span data-ttu-id="dc561-120">Настроенный <xref:System.Net.Http.HttpClient> затем используется для выполнения запросов с помощью простого шаблона [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .</span><span class="sxs-lookup"><span data-stu-id="dc561-120">The configured <xref:System.Net.Http.HttpClient> is then used to make authorized requests using a simple [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) pattern.</span></span>

<span data-ttu-id="dc561-121">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="dc561-121">`FetchData` component (*Pages/FetchData.razor*):</span></span>

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

## <a name="typed-httpclient"></a><span data-ttu-id="dc561-122">Типизированный класс HttpClient</span><span class="sxs-lookup"><span data-stu-id="dc561-122">Typed HttpClient</span></span>

<span data-ttu-id="dc561-123">Можно определить типизированный клиент, который обрабатывает все проблемы получения HTTP и маркеров в рамках одного класса.</span><span class="sxs-lookup"><span data-stu-id="dc561-123">A typed client can be defined that handles all of the HTTP and token acquisition concerns within a single class.</span></span>

<span data-ttu-id="dc561-124">*WeatherForecastClient.cs*:</span><span class="sxs-lookup"><span data-stu-id="dc561-124">*WeatherForecastClient.cs*:</span></span>

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

<span data-ttu-id="dc561-125">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="dc561-125">`Program.Main` (*Program.cs*):</span></span>

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

<span data-ttu-id="dc561-126">Компонент `FetchData` (*Pages/FetchData.razor*):</span><span class="sxs-lookup"><span data-stu-id="dc561-126">`FetchData` component (*Pages/FetchData.razor*):</span></span>

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a><span data-ttu-id="dc561-127">Настройка обработчика HttpClient</span><span class="sxs-lookup"><span data-stu-id="dc561-127">Configure the HttpClient handler</span></span>

<span data-ttu-id="dc561-128">Обработчик можно дополнительно настроить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.</span><span class="sxs-lookup"><span data-stu-id="dc561-128">The handler can be further configured with <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> for outbound HTTP requests.</span></span>

<span data-ttu-id="dc561-129">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="dc561-129">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a><span data-ttu-id="dc561-130">Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию</span><span class="sxs-lookup"><span data-stu-id="dc561-130">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>

<span data-ttu-id="dc561-131">Если приложение веб- Blazor сборки обычно использует безопасное значение по умолчанию <xref:System.Net.Http.HttpClient> , оно также может выполнять непроверенные или неавторизованные запросы веб-API, настроив именованную <xref:System.Net.Http.HttpClient> :</span><span class="sxs-lookup"><span data-stu-id="dc561-131">If the Blazor WebAssembly app ordinarily uses a secure default <xref:System.Net.Http.HttpClient>, the app can also make unauthenticated or unauthorized web API requests by configuring a named <xref:System.Net.Http.HttpClient>:</span></span>

<span data-ttu-id="dc561-132">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="dc561-132">`Program.Main` (*Program.cs*):</span></span>

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

<span data-ttu-id="dc561-133">Приведенная выше регистрация является дополнением к существующей безопасной регистрации по умолчанию <xref:System.Net.Http.HttpClient> .</span><span class="sxs-lookup"><span data-stu-id="dc561-133">The preceding registration is in addition to the existing secure default <xref:System.Net.Http.HttpClient> registration.</span></span>

<span data-ttu-id="dc561-134">Компонент создает <xref:System.Net.Http.HttpClient> из в <xref:System.Net.Http.IHttpClientFactory> для создания непроверенных или неавторизованных запросов:</span><span class="sxs-lookup"><span data-stu-id="dc561-134">A component creates the <xref:System.Net.Http.HttpClient> from the <xref:System.Net.Http.IHttpClientFactory> to make unauthenticated or unauthorized requests:</span></span>

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
> <span data-ttu-id="dc561-135">Контроллер в API сервера `WeatherForecastNoAuthenticationController` для предыдущего примера не помечен [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибутом.</span><span class="sxs-lookup"><span data-stu-id="dc561-135">The controller in the server API, `WeatherForecastNoAuthenticationController` for the preceding example, isn't marked with the [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) attribute.</span></span>

## <a name="request-additional-access-tokens"></a><span data-ttu-id="dc561-136">Запрос дополнительных маркеров доступа</span><span class="sxs-lookup"><span data-stu-id="dc561-136">Request additional access tokens</span></span>

<span data-ttu-id="dc561-137">Маркеры доступа могут быть получены вручную путем вызова `IAccessTokenProvider.RequestAccessToken` .</span><span class="sxs-lookup"><span data-stu-id="dc561-137">Access tokens can be manually obtained by calling `IAccessTokenProvider.RequestAccessToken`.</span></span>

<span data-ttu-id="dc561-138">В следующем примере дополнительные Azure Active Directory (AAD) Microsoft Graph области API необходимы приложениям для чтения данных пользователей и отправки почты.</span><span class="sxs-lookup"><span data-stu-id="dc561-138">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="dc561-139">После добавления разрешений Microsoft Graph API на портале Azure AAD дополнительные области настраиваются в клиентском приложении.</span><span class="sxs-lookup"><span data-stu-id="dc561-139">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app.</span></span>

<span data-ttu-id="dc561-140">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="dc561-140">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="dc561-141">`IAccessTokenProvider.RequestToken`Метод предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с заданным набором областей.</span><span class="sxs-lookup"><span data-stu-id="dc561-141">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision an access token with a given set of scopes.</span></span>

<span data-ttu-id="dc561-142">В Razor компоненте:</span><span class="sxs-lookup"><span data-stu-id="dc561-142">In a Razor component:</span></span>

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

<span data-ttu-id="dc561-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>возврата</span><span class="sxs-lookup"><span data-stu-id="dc561-143"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> returns:</span></span>

* <span data-ttu-id="dc561-144">`true`с помощью `token` для использования.</span><span class="sxs-lookup"><span data-stu-id="dc561-144">`true` with the `token` for use.</span></span>
* <span data-ttu-id="dc561-145">`false`значение, если маркер не извлекается.</span><span class="sxs-lookup"><span data-stu-id="dc561-145">`false` if the token isn't retrieved.</span></span>

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a><span data-ttu-id="dc561-146">HttpClient и HttpRequestMessage с параметрами запроса API FETCH</span><span class="sxs-lookup"><span data-stu-id="dc561-146">HttpClient and HttpRequestMessage with Fetch API request options</span></span>

<span data-ttu-id="dc561-147">При запуске в сборке в Blazor приложении [HttpClient](xref:fundamentals/http-requests) и <xref:System.Net.Http.HttpRequestMessage> может использоваться для настройки запросов.</span><span class="sxs-lookup"><span data-stu-id="dc561-147">When running on WebAssembly in a Blazor WebAssembly app, [HttpClient](xref:fundamentals/http-requests) and <xref:System.Net.Http.HttpRequestMessage> can be used to customize requests.</span></span> <span data-ttu-id="dc561-148">Например, можно указать метод HTTP и заголовки запроса.</span><span class="sxs-lookup"><span data-stu-id="dc561-148">For example, you can specify the HTTP method and request headers.</span></span> <span data-ttu-id="dc561-149">Следующий компонент выполняет запрос к `POST` конечной точке API To Do List на сервере и отображает текст ответа:</span><span class="sxs-lookup"><span data-stu-id="dc561-149">The following component makes a `POST` request to a To Do List API endpoint on the server and shows the response body:</span></span>

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

<span data-ttu-id="dc561-150">Реализация <xref:System.Net.Http.HttpClient> [виндоворворкерглобалскопе. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)в .NET-сборке использует.</span><span class="sxs-lookup"><span data-stu-id="dc561-150">.NET WebAssembly's implementation of <xref:System.Net.Http.HttpClient> uses [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch).</span></span> <span data-ttu-id="dc561-151">Выборка позволяет настроить несколько [параметров для конкретного запроса](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="dc561-151">Fetch allows configuring several [request-specific options](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span> 

<span data-ttu-id="dc561-152">Параметры запроса HTTP FETCH можно настроить с помощью <xref:System.Net.Http.HttpRequestMessage> методов расширения, приведенных в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dc561-152">HTTP fetch request options can be configured with <xref:System.Net.Http.HttpRequestMessage> extension methods shown in the following table.</span></span>

| <span data-ttu-id="dc561-153">Метод расширения</span><span class="sxs-lookup"><span data-stu-id="dc561-153">Extension method</span></span> | <span data-ttu-id="dc561-154">Получение свойства запроса</span><span class="sxs-lookup"><span data-stu-id="dc561-154">Fetch request property</span></span> |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [<span data-ttu-id="dc561-155">информации</span><span class="sxs-lookup"><span data-stu-id="dc561-155">credentials</span></span>](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [<span data-ttu-id="dc561-156">Мбайта</span><span class="sxs-lookup"><span data-stu-id="dc561-156">cache</span></span>](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [<span data-ttu-id="dc561-157">mode</span><span class="sxs-lookup"><span data-stu-id="dc561-157">mode</span></span>](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [<span data-ttu-id="dc561-158">контроля</span><span class="sxs-lookup"><span data-stu-id="dc561-158">integrity</span></span>](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

<span data-ttu-id="dc561-159">Можно задать дополнительные параметры с помощью более универсального <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> метода расширения.</span><span class="sxs-lookup"><span data-stu-id="dc561-159">You can set additional options using the more generic <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> extension method.</span></span>
 
<span data-ttu-id="dc561-160">HTTP-ответ обычно замещается в буфер в Blazor приложении сборки, чтобы обеспечить поддержку синхронных операций чтения содержимого ответа.</span><span class="sxs-lookup"><span data-stu-id="dc561-160">The HTTP response is typically buffered in a Blazor WebAssembly app to enable support for sync reads on the response content.</span></span> <span data-ttu-id="dc561-161">Чтобы включить поддержку потоковой передачи ответов, используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> метод расширения для запроса.</span><span class="sxs-lookup"><span data-stu-id="dc561-161">To enable support for response streaming, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> extension method on the request.</span></span>

<span data-ttu-id="dc561-162">Чтобы включить учетные данные в запрос между источниками, используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> метод расширения:</span><span class="sxs-lookup"><span data-stu-id="dc561-162">To include credentials in a cross-origin request, use the <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> extension method:</span></span>

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

<span data-ttu-id="dc561-163">Дополнительные сведения о возможностях API-получения см. в разделе [MDN Web документация: виндоворворкерглобалскопе. fetch ():P араметерс](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span><span class="sxs-lookup"><span data-stu-id="dc561-163">For more information on Fetch API options, see [MDN web docs: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).</span></span>

<span data-ttu-id="dc561-164">При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS этот `Authorization` заголовок должен быть разрешен ПОЛИТИКОЙ CORS.</span><span class="sxs-lookup"><span data-stu-id="dc561-164">When sending credentials (authorization cookies/headers) on CORS requests, the `Authorization` header must be allowed by the CORS policy.</span></span>

<span data-ttu-id="dc561-165">Следующая политика включает в себя настройку для:</span><span class="sxs-lookup"><span data-stu-id="dc561-165">The following policy includes configuration for:</span></span>

* <span data-ttu-id="dc561-166">Источники запроса ( `http://localhost:5000` , `https://localhost:5001` ).</span><span class="sxs-lookup"><span data-stu-id="dc561-166">Request origins (`http://localhost:5000`, `https://localhost:5001`).</span></span>
* <span data-ttu-id="dc561-167">Любой метод (глагол).</span><span class="sxs-lookup"><span data-stu-id="dc561-167">Any method (verb).</span></span>
* <span data-ttu-id="dc561-168">`Content-Type`и `Authorization` заголовки.</span><span class="sxs-lookup"><span data-stu-id="dc561-168">`Content-Type` and `Authorization` headers.</span></span> <span data-ttu-id="dc561-169">Чтобы разрешить пользовательский заголовок (например, `x-custom-header` ), выведите список заголовков при вызове метода <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .</span><span class="sxs-lookup"><span data-stu-id="dc561-169">To allow a custom header (for example, `x-custom-header`), list the header when calling <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.</span></span>
* <span data-ttu-id="dc561-170">Учетные данные, которые задаются кодом JavaScript на стороне клиента ( `credentials` свойство имеет значение `include` ).</span><span class="sxs-lookup"><span data-stu-id="dc561-170">Credentials set by client-side JavaScript code (`credentials` property set to `include`).</span></span>

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

<span data-ttu-id="dc561-171">Дополнительные сведения см <xref:security/cors> . в разделе и компоненте тестера HTTP-запросов примера приложения (*Components/хттпрекуесттестер. Razor*).</span><span class="sxs-lookup"><span data-stu-id="dc561-171">For more information, see <xref:security/cors> and the sample app's HTTP Request Tester component (*Components/HTTPRequestTester.razor*).</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="dc561-172">Обработку ошибок запросов маркера</span><span class="sxs-lookup"><span data-stu-id="dc561-172">Handle token request errors</span></span>

<span data-ttu-id="dc561-173">Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в рамках SPA и в Identity поставщике (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.</span><span class="sxs-lookup"><span data-stu-id="dc561-173">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="dc561-174">Маркеры, которые IP-адрес выдает пользователю, обычно действительны в течение короткого периода времени, примерно один час, поэтому клиентское приложение должно регулярно получать новые токены.</span><span class="sxs-lookup"><span data-stu-id="dc561-174">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="dc561-175">В противном случае пользователь будет зарегистрирован после истечения срока действия назначенных токенов.</span><span class="sxs-lookup"><span data-stu-id="dc561-175">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="dc561-176">В большинстве случаев клиенты OIDC могут подготавливать новые маркеры, не требуя от пользователя повторной проверки подлинности благодаря состоянию аутентификации или сеансу, который хранится в IP-адресе.</span><span class="sxs-lookup"><span data-stu-id="dc561-176">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="dc561-177">В некоторых случаях клиент не может получить маркер без взаимодействия с пользователем, например, когда по какой-либо причине пользователь явно выходит из IP.</span><span class="sxs-lookup"><span data-stu-id="dc561-177">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="dc561-178">Эта ситуация возникает при посещении `https://login.microsoftonline.com` и выходе пользователя из систему. В этих сценариях приложение не знает о немедленном выходе пользователя из систему. Любой токен, который удерживает клиент, может быть недействительным.</span><span class="sxs-lookup"><span data-stu-id="dc561-178">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="dc561-179">Кроме того, клиент не может подготавливать новый маркер без взаимодействия с пользователем после истечения срока действия текущего маркера.</span><span class="sxs-lookup"><span data-stu-id="dc561-179">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="dc561-180">Эти сценарии не относятся к проверке подлинности на основе маркеров.</span><span class="sxs-lookup"><span data-stu-id="dc561-180">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="dc561-181">Они являются частью природы одностраничные приложения.</span><span class="sxs-lookup"><span data-stu-id="dc561-181">They are part of the nature of SPAs.</span></span> <span data-ttu-id="dc561-182">Проверка подлинности с помощью файлов cookie также не вызывает API сервера, если удаляется файл cookie для аутентификации.</span><span class="sxs-lookup"><span data-stu-id="dc561-182">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="dc561-183">Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:</span><span class="sxs-lookup"><span data-stu-id="dc561-183">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="dc561-184">Чтобы создать новый маркер доступа для вызова API, пользователю может потребоваться выполнить повторную проверку подлинности.</span><span class="sxs-lookup"><span data-stu-id="dc561-184">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="dc561-185">Даже если у клиента есть токен, который кажется допустимым, вызов сервера может завершиться ошибкой, так как маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="dc561-185">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="dc561-186">Когда приложение запрашивает маркер, существует два возможных результата:</span><span class="sxs-lookup"><span data-stu-id="dc561-186">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="dc561-187">Запрос выполнен успешно, и приложение имеет действительный токен.</span><span class="sxs-lookup"><span data-stu-id="dc561-187">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="dc561-188">Запрос завершается ошибкой, и приложение должно повторно пройти проверку подлинности пользователя, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="dc561-188">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="dc561-189">При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="dc561-189">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="dc561-190">Существует несколько подходов, повышающих уровень сложности:</span><span class="sxs-lookup"><span data-stu-id="dc561-190">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="dc561-191">Сохранение текущего состояния страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="dc561-191">Store the current page state in session storage.</span></span> <span data-ttu-id="dc561-192">Во время [события жизненного цикла онинитиализедасинк](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) Проверьте, можно ли восстановить состояние, прежде чем продолжить.</span><span class="sxs-lookup"><span data-stu-id="dc561-192">During the [OnInitializedAsync lifecycle event](xref:blazor/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>), check if state can be restored before continuing.</span></span>
* <span data-ttu-id="dc561-193">Добавьте параметр строки запроса и используйте его в качестве способа сигнализации приложению о необходимости повторного расконсервации ранее сохраненного состояния.</span><span class="sxs-lookup"><span data-stu-id="dc561-193">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="dc561-194">Добавьте параметр строки запроса с уникальным идентификатором для хранения данных в хранилище сеанса без риска конфликтов с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="dc561-194">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="dc561-195">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="dc561-195">The following example shows how to:</span></span>

* <span data-ttu-id="dc561-196">Сохраните состояние перед перенаправлением на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="dc561-196">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="dc561-197">Восстановите предыдущее состояние после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="dc561-197">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="dc561-198">Сохранить состояние приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dc561-198">Save app state before an authentication operation</span></span>

<span data-ttu-id="dc561-199">Во время операции аутентификации существуют случаи, когда необходимо сохранить состояние приложения, прежде чем браузер перенаправится на IP-адрес.</span><span class="sxs-lookup"><span data-stu-id="dc561-199">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="dc561-200">Это может быть так, если вы используете нечто вроде контейнера состояния и хотите восстановить состояние после завершения проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dc561-200">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="dc561-201">Можно использовать пользовательский объект состояния проверки подлинности, чтобы сохранить состояние конкретного приложения или ссылку на него, а затем восстановить это состояние после успешного завершения операции проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dc561-201">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="dc561-202">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="dc561-202">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/authentication/{action}"
@inject JSRuntime JS
@inject StateContainer State
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorViewCore Action="@Action" 
    AuthenticationState="AuthenticationState" OnLogInSucceeded="RestoreState" 
    OnLogOutSucceeded="RestoreState" />

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

## <a name="customize-app-routes"></a><span data-ttu-id="dc561-203">Настройка маршрутов приложений</span><span class="sxs-lookup"><span data-stu-id="dc561-203">Customize app routes</span></span>

<span data-ttu-id="dc561-204">По умолчанию библиотека [Microsoft. AspNetCore. Components. веб-сборка. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) использует маршруты, приведенные в следующей таблице, для представления различных состояний проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dc561-204">By default, the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="dc561-205">Маршрут</span><span class="sxs-lookup"><span data-stu-id="dc561-205">Route</span></span>                            | <span data-ttu-id="dc561-206">Назначение</span><span class="sxs-lookup"><span data-stu-id="dc561-206">Purpose</span></span> |
| ---
<span data-ttu-id="dc561-207">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-207">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-208">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-209">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-209">'Blazor'</span></span>
- <span data-ttu-id="dc561-210">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-210">'Identity'</span></span>
- <span data-ttu-id="dc561-211">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-211">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-212">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-212">'Razor'</span></span>
- <span data-ttu-id="dc561-213">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-213">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-214">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-214">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-215">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-216">'Blazor'</span></span>
- <span data-ttu-id="dc561-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-217">'Identity'</span></span>
- <span data-ttu-id="dc561-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-219">'Razor'</span></span>
- <span data-ttu-id="dc561-220">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-221">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-221">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-222">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-223">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-223">'Blazor'</span></span>
- <span data-ttu-id="dc561-224">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-224">'Identity'</span></span>
- <span data-ttu-id="dc561-225">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-225">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-226">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-226">'Razor'</span></span>
- <span data-ttu-id="dc561-227">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-227">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-228">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-228">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-229">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-230">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-230">'Blazor'</span></span>
- <span data-ttu-id="dc561-231">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-231">'Identity'</span></span>
- <span data-ttu-id="dc561-232">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-232">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-233">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-233">'Razor'</span></span>
- <span data-ttu-id="dc561-234">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-234">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-235">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-235">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-236">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-237">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-237">'Blazor'</span></span>
- <span data-ttu-id="dc561-238">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-238">'Identity'</span></span>
- <span data-ttu-id="dc561-239">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-239">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-240">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-240">'Razor'</span></span>
- <span data-ttu-id="dc561-241">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-241">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-242">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-242">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-243">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-244">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-244">'Blazor'</span></span>
- <span data-ttu-id="dc561-245">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-245">'Identity'</span></span>
- <span data-ttu-id="dc561-246">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-246">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-247">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-247">'Razor'</span></span>
- <span data-ttu-id="dc561-248">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-248">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-249">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-249">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-250">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-251">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-251">'Blazor'</span></span>
- <span data-ttu-id="dc561-252">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-252">'Identity'</span></span>
- <span data-ttu-id="dc561-253">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-253">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-254">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-254">'Razor'</span></span>
- <span data-ttu-id="dc561-255">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-255">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-256">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-256">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-257">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-258">'Blazor'</span></span>
- <span data-ttu-id="dc561-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-259">'Identity'</span></span>
- <span data-ttu-id="dc561-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-261">'Razor'</span></span>
- <span data-ttu-id="dc561-262">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-262">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-263">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-263">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-264">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-265">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-265">'Blazor'</span></span>
- <span data-ttu-id="dc561-266">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-266">'Identity'</span></span>
- <span data-ttu-id="dc561-267">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-267">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-268">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-268">'Razor'</span></span>
- <span data-ttu-id="dc561-269">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-269">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-270">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-270">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-271">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-272">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-272">'Blazor'</span></span>
- <span data-ttu-id="dc561-273">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-273">'Identity'</span></span>
- <span data-ttu-id="dc561-274">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-274">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-275">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-275">'Razor'</span></span>
- <span data-ttu-id="dc561-276">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-276">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-277">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-277">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-278">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-279">'Blazor'</span></span>
- <span data-ttu-id="dc561-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-280">'Identity'</span></span>
- <span data-ttu-id="dc561-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-282">'Razor'</span></span>
- <span data-ttu-id="dc561-283">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-283">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-284">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-284">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-285">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-286">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-286">'Blazor'</span></span>
- <span data-ttu-id="dc561-287">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-287">'Identity'</span></span>
- <span data-ttu-id="dc561-288">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-288">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-289">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-289">'Razor'</span></span>
- <span data-ttu-id="dc561-290">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-290">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-291">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-291">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-292">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-293">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-293">'Blazor'</span></span>
- <span data-ttu-id="dc561-294">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-294">'Identity'</span></span>
- <span data-ttu-id="dc561-295">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-295">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-296">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-296">'Razor'</span></span>
- <span data-ttu-id="dc561-297">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-297">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-298">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-298">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-299">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-300">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-300">'Blazor'</span></span>
- <span data-ttu-id="dc561-301">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-301">'Identity'</span></span>
- <span data-ttu-id="dc561-302">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-302">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-303">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-303">'Razor'</span></span>
- <span data-ttu-id="dc561-304">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-304">'SignalR' uid:</span></span> 

<span data-ttu-id="dc561-305">---------------- | Заголовок---: "ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки" author: Description: "Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.</span><span class="sxs-lookup"><span data-stu-id="dc561-305">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-306">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-307">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-307">'Blazor'</span></span>
- <span data-ttu-id="dc561-308">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-308">'Identity'</span></span>
- <span data-ttu-id="dc561-309">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-309">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-310">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-310">'Razor'</span></span>
- <span data-ttu-id="dc561-311">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-311">'SignalR' uid:</span></span> 

<span data-ttu-id="dc561-312">---- | | `authentication/login`           | Активирует операцию входа.</span><span class="sxs-lookup"><span data-stu-id="dc561-312">---- | | `authentication/login`           | Triggers a sign-in operation.</span></span> <span data-ttu-id="dc561-313">| | `authentication/login-callback`  | Обрабатывает результат любой операции входа.</span><span class="sxs-lookup"><span data-stu-id="dc561-313">| | `authentication/login-callback`  | Handles the result of any sign-in operation.</span></span> <span data-ttu-id="dc561-314">| | `authentication/login-failed`    | Отображает сообщения об ошибках при сбое операции входа по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="dc561-314">| | `authentication/login-failed`    | Displays error messages when the sign-in operation fails for some reason.</span></span> <span data-ttu-id="dc561-315">| | `authentication/logout`          | Активирует операцию выхода.</span><span class="sxs-lookup"><span data-stu-id="dc561-315">| | `authentication/logout`          | Triggers a sign-out operation.</span></span> <span data-ttu-id="dc561-316">| | `authentication/logout-callback` | Обрабатывает результат операции выхода.</span><span class="sxs-lookup"><span data-stu-id="dc561-316">| | `authentication/logout-callback` | Handles the result of a sign-out operation.</span></span> <span data-ttu-id="dc561-317">| | `authentication/logout-failed`   | Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине.</span><span class="sxs-lookup"><span data-stu-id="dc561-317">| | `authentication/logout-failed`   | Displays error messages when the sign-out operation fails for some reason.</span></span> <span data-ttu-id="dc561-318">| | `authentication/logged-out`      | Указывает, что пользователь успешно выполнил выход.</span><span class="sxs-lookup"><span data-stu-id="dc561-318">| | `authentication/logged-out`      | Indicates that the user has successfully logout.</span></span> <span data-ttu-id="dc561-319">| | `authentication/profile`         | Активирует операцию для изменения профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="dc561-319">| | `authentication/profile`         | Triggers an operation to edit the user profile.</span></span> <span data-ttu-id="dc561-320">| | `authentication/register`        | Активирует операцию для регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="dc561-320">| | `authentication/register`        | Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="dc561-321">Маршруты, показанные в предыдущей таблице, можно настроить с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="dc561-321">The routes shown in the preceding table are configurable via <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>.</span></span> <span data-ttu-id="dc561-322">При настройке параметров для предоставления настраиваемых маршрутов убедитесь, что у приложения есть маршрут, который обрабатывает каждый путь.</span><span class="sxs-lookup"><span data-stu-id="dc561-322">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="dc561-323">В следующем примере все пути имеют префикс `/security` .</span><span class="sxs-lookup"><span data-stu-id="dc561-323">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="dc561-324">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="dc561-324">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="dc561-325">`Program.Main` (*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="dc561-325">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="dc561-326">Если требование вызывается для совершенно разных путей, задайте маршруты, как описано выше, и выполните визуализацию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> с помощью явного параметра Action:</span><span class="sxs-lookup"><span data-stu-id="dc561-326">If the requirement calls for completely different paths, set the routes as described previously and render the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="dc561-327">При необходимости вы можете разбить пользовательский интерфейс на разные страницы.</span><span class="sxs-lookup"><span data-stu-id="dc561-327">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="dc561-328">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dc561-328">Customize the authentication user interface</span></span>

<span data-ttu-id="dc561-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>включает набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dc561-329"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="dc561-330">Каждое состояние можно настроить путем передачи пользовательского <xref:Microsoft.AspNetCore.Components.RenderFragment> .</span><span class="sxs-lookup"><span data-stu-id="dc561-330">Each state can be customized by passing in a custom <xref:Microsoft.AspNetCore.Components.RenderFragment>.</span></span> <span data-ttu-id="dc561-331">Для настройки отображаемого текста во время первоначального входа в систему может измениться <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> следующим образом.</span><span class="sxs-lookup"><span data-stu-id="dc561-331">To customize the displayed text during the initial login process, can change the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> as follows.</span></span>

<span data-ttu-id="dc561-332">`Authentication`Component (*pages/Authentication. Razor*):</span><span class="sxs-lookup"><span data-stu-id="dc561-332">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="dc561-333">В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> имеется один фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="dc561-333">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="dc561-334">Маршрут</span><span class="sxs-lookup"><span data-stu-id="dc561-334">Route</span></span>                            | <span data-ttu-id="dc561-335">Fragment (Фрагмент)</span><span class="sxs-lookup"><span data-stu-id="dc561-335">Fragment</span></span>                |
| ---
<span data-ttu-id="dc561-336">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-336">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-337">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-338">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-338">'Blazor'</span></span>
- <span data-ttu-id="dc561-339">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-339">'Identity'</span></span>
- <span data-ttu-id="dc561-340">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-340">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-341">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-341">'Razor'</span></span>
- <span data-ttu-id="dc561-342">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-342">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-343">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-343">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-344">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-345">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-345">'Blazor'</span></span>
- <span data-ttu-id="dc561-346">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-346">'Identity'</span></span>
- <span data-ttu-id="dc561-347">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-347">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-348">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-348">'Razor'</span></span>
- <span data-ttu-id="dc561-349">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-349">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-350">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-350">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-351">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-352">'Blazor'</span></span>
- <span data-ttu-id="dc561-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-353">'Identity'</span></span>
- <span data-ttu-id="dc561-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-355">'Razor'</span></span>
- <span data-ttu-id="dc561-356">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-357">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-357">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-358">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-359">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-359">'Blazor'</span></span>
- <span data-ttu-id="dc561-360">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-360">'Identity'</span></span>
- <span data-ttu-id="dc561-361">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-361">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-362">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-362">'Razor'</span></span>
- <span data-ttu-id="dc561-363">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-363">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-364">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-364">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-365">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-366">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-366">'Blazor'</span></span>
- <span data-ttu-id="dc561-367">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-367">'Identity'</span></span>
- <span data-ttu-id="dc561-368">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-368">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-369">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-369">'Razor'</span></span>
- <span data-ttu-id="dc561-370">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-370">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-371">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-371">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-372">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-373">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-373">'Blazor'</span></span>
- <span data-ttu-id="dc561-374">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-374">'Identity'</span></span>
- <span data-ttu-id="dc561-375">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-375">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-376">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-376">'Razor'</span></span>
- <span data-ttu-id="dc561-377">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-377">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-378">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-378">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-379">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-380">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-380">'Blazor'</span></span>
- <span data-ttu-id="dc561-381">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-381">'Identity'</span></span>
- <span data-ttu-id="dc561-382">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-382">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-383">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-383">'Razor'</span></span>
- <span data-ttu-id="dc561-384">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-384">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-385">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-385">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-386">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-387">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-387">'Blazor'</span></span>
- <span data-ttu-id="dc561-388">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-388">'Identity'</span></span>
- <span data-ttu-id="dc561-389">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-389">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-390">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-390">'Razor'</span></span>
- <span data-ttu-id="dc561-391">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-391">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-392">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-392">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-393">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-394">'Blazor'</span></span>
- <span data-ttu-id="dc561-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-395">'Identity'</span></span>
- <span data-ttu-id="dc561-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-397">'Razor'</span></span>
- <span data-ttu-id="dc561-398">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-399">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-399">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-400">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-401">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-401">'Blazor'</span></span>
- <span data-ttu-id="dc561-402">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-402">'Identity'</span></span>
- <span data-ttu-id="dc561-403">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-403">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-404">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-404">'Razor'</span></span>
- <span data-ttu-id="dc561-405">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-405">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-406">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-406">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-407">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-408">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-408">'Blazor'</span></span>
- <span data-ttu-id="dc561-409">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-409">'Identity'</span></span>
- <span data-ttu-id="dc561-410">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-410">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-411">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-411">'Razor'</span></span>
- <span data-ttu-id="dc561-412">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-412">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-413">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-413">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-414">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-415">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-415">'Blazor'</span></span>
- <span data-ttu-id="dc561-416">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-416">'Identity'</span></span>
- <span data-ttu-id="dc561-417">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-417">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-418">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-418">'Razor'</span></span>
- <span data-ttu-id="dc561-419">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-419">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-420">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-420">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-421">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-422">'Blazor'</span></span>
- <span data-ttu-id="dc561-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-423">'Identity'</span></span>
- <span data-ttu-id="dc561-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-425">'Razor'</span></span>
- <span data-ttu-id="dc561-426">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-427">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-427">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-428">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-429">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-429">'Blazor'</span></span>
- <span data-ttu-id="dc561-430">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-430">'Identity'</span></span>
- <span data-ttu-id="dc561-431">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-431">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-432">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-432">'Razor'</span></span>
- <span data-ttu-id="dc561-433">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-433">'SignalR' uid:</span></span> 

<span data-ttu-id="dc561-434">---------------- | Заголовок---: "ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки" author: Description: "Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.</span><span class="sxs-lookup"><span data-stu-id="dc561-434">---------------- | --- title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-435">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-436">'Blazor'</span></span>
- <span data-ttu-id="dc561-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-437">'Identity'</span></span>
- <span data-ttu-id="dc561-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-439">'Razor'</span></span>
- <span data-ttu-id="dc561-440">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-440">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-441">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-441">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-442">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-443">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-443">'Blazor'</span></span>
- <span data-ttu-id="dc561-444">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-444">'Identity'</span></span>
- <span data-ttu-id="dc561-445">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-445">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-446">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-446">'Razor'</span></span>
- <span data-ttu-id="dc561-447">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-447">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-448">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-448">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-449">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-450">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-450">'Blazor'</span></span>
- <span data-ttu-id="dc561-451">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-451">'Identity'</span></span>
- <span data-ttu-id="dc561-452">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-452">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-453">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-453">'Razor'</span></span>
- <span data-ttu-id="dc561-454">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-454">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-455">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-455">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-456">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-457">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-457">'Blazor'</span></span>
- <span data-ttu-id="dc561-458">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-458">'Identity'</span></span>
- <span data-ttu-id="dc561-459">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-459">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-460">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-460">'Razor'</span></span>
- <span data-ttu-id="dc561-461">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-461">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-462">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-462">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-463">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-464">'Blazor'</span></span>
- <span data-ttu-id="dc561-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-465">'Identity'</span></span>
- <span data-ttu-id="dc561-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-467">'Razor'</span></span>
- <span data-ttu-id="dc561-468">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-469">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-469">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-470">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-471">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-471">'Blazor'</span></span>
- <span data-ttu-id="dc561-472">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-472">'Identity'</span></span>
- <span data-ttu-id="dc561-473">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-473">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-474">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-474">'Razor'</span></span>
- <span data-ttu-id="dc561-475">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-475">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-476">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-476">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-477">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-478">'Blazor'</span></span>
- <span data-ttu-id="dc561-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-479">'Identity'</span></span>
- <span data-ttu-id="dc561-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-481">'Razor'</span></span>
- <span data-ttu-id="dc561-482">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-483">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-483">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-484">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-485">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-485">'Blazor'</span></span>
- <span data-ttu-id="dc561-486">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-486">'Identity'</span></span>
- <span data-ttu-id="dc561-487">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-487">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-488">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-488">'Razor'</span></span>
- <span data-ttu-id="dc561-489">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-489">'SignalR' uid:</span></span> 

-
<span data-ttu-id="dc561-490">Title: ' ASP.NET Core Blazor Дополнительные сценарии безопасности для сборки ' author: Description: ' Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности '.</span><span class="sxs-lookup"><span data-stu-id="dc561-490">title: 'ASP.NET Core Blazor WebAssembly additional security scenarios' author: description: 'Learn how to configure Blazor WebAssembly for additional security scenarios.'</span></span>
<span data-ttu-id="dc561-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="dc561-491">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="dc561-492">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="dc561-492">'Blazor'</span></span>
- <span data-ttu-id="dc561-493">'Identity'</span><span class="sxs-lookup"><span data-stu-id="dc561-493">'Identity'</span></span>
- <span data-ttu-id="dc561-494">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="dc561-494">'Let's Encrypt'</span></span>
- <span data-ttu-id="dc561-495">'Razor'</span><span class="sxs-lookup"><span data-stu-id="dc561-495">'Razor'</span></span>
- <span data-ttu-id="dc561-496">ИД пользователя "SignalR":</span><span class="sxs-lookup"><span data-stu-id="dc561-496">'SignalR' uid:</span></span> 

<span data-ttu-id="dc561-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span><span class="sxs-lookup"><span data-stu-id="dc561-497">------------ | | `authentication/login`           | `<LoggingIn>`           | | `authentication/login-callback`  | `<CompletingLoggingIn>` | | `authentication/login-failed`    | `<LogInFailed>`         | | `authentication/logout`          | `<LogOut>`              | | `authentication/logout-callback` | `<CompletingLogOut>`    | | `authentication/logout-failed`   | `<LogOutFailed>`        | | `authentication/logged-out`      | `<LogOutSucceeded>`     | | `authentication/profile`         | `<UserProfile>`         | | `authentication/register`        | `<Registering>`         |</span></span>

## <a name="customize-the-user"></a><span data-ttu-id="dc561-498">Настройка пользователя</span><span class="sxs-lookup"><span data-stu-id="dc561-498">Customize the user</span></span>

<span data-ttu-id="dc561-499">Пользователи, привязанные к приложению, могут быть настроены.</span><span class="sxs-lookup"><span data-stu-id="dc561-499">Users bound to the app can be customized.</span></span> <span data-ttu-id="dc561-500">В следующем примере все пользователи, прошедшие проверку подлинности, получают `amr` утверждение для каждого из методов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="dc561-500">In the following example, all authenticated users receive an `amr` claim for each of the user's authentication methods.</span></span>

<span data-ttu-id="dc561-501">Создайте класс, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> класс:</span><span class="sxs-lookup"><span data-stu-id="dc561-501">Create a class that extends the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> class:</span></span>

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

<span data-ttu-id="dc561-502">Создайте фабрику, которая расширяет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :</span><span class="sxs-lookup"><span data-stu-id="dc561-502">Create a factory that extends <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:</span></span>

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

<span data-ttu-id="dc561-503">Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dc561-503">Register the `CustomAccountFactory` for the authentication provider in use.</span></span> <span data-ttu-id="dc561-504">Любая из следующих регистраций допустима:</span><span class="sxs-lookup"><span data-stu-id="dc561-504">Any of the following registrations are valid:</span></span> 

* <span data-ttu-id="dc561-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="dc561-505"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:</span></span>

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

* <span data-ttu-id="dc561-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span><span class="sxs-lookup"><span data-stu-id="dc561-506"><xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:</span></span>

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
  
* <span data-ttu-id="dc561-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span><span class="sxs-lookup"><span data-stu-id="dc561-507"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:</span></span>

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

## <a name="support-prerendering-with-authentication"></a><span data-ttu-id="dc561-508">Поддержка предварительной отрисовки с помощью проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="dc561-508">Support prerendering with authentication</span></span>

<span data-ttu-id="dc561-509">Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:</span><span class="sxs-lookup"><span data-stu-id="dc561-509">After following the guidance in one of the hosted Blazor WebAssembly app topics, use the following instructions to create an app that:</span></span>

* <span data-ttu-id="dc561-510">предварительно отрисовывает пути, не требующие авторизации;</span><span class="sxs-lookup"><span data-stu-id="dc561-510">Prerenders paths for which authorization isn't required.</span></span>
* <span data-ttu-id="dc561-511">не выполняет предварительную отрисовку путей, требующих авторизации.</span><span class="sxs-lookup"><span data-stu-id="dc561-511">Doesn't prerender paths for which authorization is required.</span></span>

<span data-ttu-id="dc561-512">В классе `Program` клиентского приложения (*Program.cs*) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):</span><span class="sxs-lookup"><span data-stu-id="dc561-512">In the Client app's `Program` class (*Program.cs*), factor common service registrations into a separate method (for example, `ConfigureCommonServices`):</span></span>

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

<span data-ttu-id="dc561-513">В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:</span><span class="sxs-lookup"><span data-stu-id="dc561-513">In the Server app's `Startup.ConfigureServices`, register the following additional services:</span></span>

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

<span data-ttu-id="dc561-514">В методе серверного приложения `Startup.Configure` замените [конечные точки. Мапфаллбакктофиле ("index. HTML")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) с [конечными точками. Мапфаллбакктопаже ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span><span class="sxs-lookup"><span data-stu-id="dc561-514">In the Server app's `Startup.Configure` method, replace [endpoints.MapFallbackToFile("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) with [endpoints.MapFallbackToPage("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):</span></span>

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

<span data-ttu-id="dc561-515">В серверном приложении создайте папку *Pages*, если она отсутствует.</span><span class="sxs-lookup"><span data-stu-id="dc561-515">In the Server app, create a *Pages* folder if it doesn't exist.</span></span> <span data-ttu-id="dc561-516">В папке *Pages* серверного приложения создайте страницу *_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="dc561-516">Create a *_Host.cshtml* page inside the Server app's *Pages* folder.</span></span> <span data-ttu-id="dc561-517">Вставьте содержимое из файла *wwwroot/index.html* клиентского приложения в файл *Pages/_Host.cshtml*.</span><span class="sxs-lookup"><span data-stu-id="dc561-517">Paste the contents from the Client app's *wwwroot/index.html* file into the *Pages/_Host.cshtml* file.</span></span> <span data-ttu-id="dc561-518">Обновите содержимое файла:</span><span class="sxs-lookup"><span data-stu-id="dc561-518">Update the file's contents:</span></span>

* <span data-ttu-id="dc561-519">Добавьте `@page "_Host"` в начало файла.</span><span class="sxs-lookup"><span data-stu-id="dc561-519">Add `@page "_Host"` to the top of the file.</span></span>
* <span data-ttu-id="dc561-520">Замените тег `<app>Loading...</app>` следующим:</span><span class="sxs-lookup"><span data-stu-id="dc561-520">Replace the `<app>Loading...</app>` tag with the following:</span></span>

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a><span data-ttu-id="dc561-521">Варианты для размещенных приложений и сторонних поставщиков входа</span><span class="sxs-lookup"><span data-stu-id="dc561-521">Options for hosted apps and third-party login providers</span></span>

<span data-ttu-id="dc561-522">При проверке подлинности и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов проверки подлинности пользователя.</span><span class="sxs-lookup"><span data-stu-id="dc561-522">When authenticating and authorizing a hosted Blazor WebAssembly app with a third-party provider, there are several options available for authenticating the user.</span></span> <span data-ttu-id="dc561-523">Выбор варианта зависит от вашего сценария.</span><span class="sxs-lookup"><span data-stu-id="dc561-523">Which one you choose depends on your scenario.</span></span>

<span data-ttu-id="dc561-524">Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.</span><span class="sxs-lookup"><span data-stu-id="dc561-524">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a><span data-ttu-id="dc561-525">Проверка подлинности пользователей для вызова только защищенных сторонних API</span><span class="sxs-lookup"><span data-stu-id="dc561-525">Authenticate users to only call protected third party APIs</span></span>

<span data-ttu-id="dc561-526">Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:</span><span class="sxs-lookup"><span data-stu-id="dc561-526">Authenticate the user with a client-side OAuth flow against the third-party API provider:</span></span>

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 <span data-ttu-id="dc561-527">В этом сценарии:</span><span class="sxs-lookup"><span data-stu-id="dc561-527">In this scenario:</span></span>

* <span data-ttu-id="dc561-528">Сервер, на котором размещено приложение, не имеет особого значения.</span><span class="sxs-lookup"><span data-stu-id="dc561-528">The server hosting the app doesn't play a role.</span></span>
* <span data-ttu-id="dc561-529">Невозможно обеспечить защиту API на сервере.</span><span class="sxs-lookup"><span data-stu-id="dc561-529">APIs on the server can't be protected.</span></span>
* <span data-ttu-id="dc561-530">Приложение может вызывать только защищенные сторонние интерфейсы API.</span><span class="sxs-lookup"><span data-stu-id="dc561-530">The app can only call protected third-party APIs.</span></span>

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a><span data-ttu-id="dc561-531">Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API</span><span class="sxs-lookup"><span data-stu-id="dc561-531">Authenticate users with a third-party provider and call protected APIs on the host server and the third party</span></span>

<span data-ttu-id="dc561-532">Настройка Identity со сторонним поставщиком входа.</span><span class="sxs-lookup"><span data-stu-id="dc561-532">Configure Identity with a third-party login provider.</span></span> <span data-ttu-id="dc561-533">Получите токены, необходимые для доступа к сторонним API, и сохраните их.</span><span class="sxs-lookup"><span data-stu-id="dc561-533">Obtain the tokens required for third-party API access and store them.</span></span>

<span data-ttu-id="dc561-534">При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="dc561-534">When a user logs in, Identity collects access and refresh tokens as part of the authentication process.</span></span> <span data-ttu-id="dc561-535">На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.</span><span class="sxs-lookup"><span data-stu-id="dc561-535">At that point, there are a couple of approaches available for making API calls to third-party APIs.</span></span>

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a><span data-ttu-id="dc561-536">Использование токена доступа сервера для получения стороннего токена доступа</span><span class="sxs-lookup"><span data-stu-id="dc561-536">Use a server access token to retrieve the third-party access token</span></span>

<span data-ttu-id="dc561-537">С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера.</span><span class="sxs-lookup"><span data-stu-id="dc561-537">Use the access token generated on the server to retrieve the third-party access token from a server API endpoint.</span></span> <span data-ttu-id="dc561-538">В нем используйте сторонний маркер доступа, чтобы вызывать сторонние ресурсы API непосредственно из Identity клиента.</span><span class="sxs-lookup"><span data-stu-id="dc561-538">From there, use the third-party access token to call third-party API resources directly from Identity on the client.</span></span>

<span data-ttu-id="dc561-539">Этот вариант использовать не рекомендуется.</span><span class="sxs-lookup"><span data-stu-id="dc561-539">We don't recommend this approach.</span></span> <span data-ttu-id="dc561-540">Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента.</span><span class="sxs-lookup"><span data-stu-id="dc561-540">This approach requires treating the third-party access token as if it were generated for a public client.</span></span> <span data-ttu-id="dc561-541">С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента.</span><span class="sxs-lookup"><span data-stu-id="dc561-541">In OAuth terms, the public app doesn't have a client secret because it can't be trusted to store secrets safely, and the access token is produced for a confidential client.</span></span> <span data-ttu-id="dc561-542">Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.</span><span class="sxs-lookup"><span data-stu-id="dc561-542">A confidential client is a client that has a client secret and is assumed to be able to safely store secrets.</span></span>

* <span data-ttu-id="dc561-543">Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.</span><span class="sxs-lookup"><span data-stu-id="dc561-543">The third-party access token might be granted additional scopes to perform sensitive operations based on the fact that the third-party emitted the token for a more trusted client.</span></span>
* <span data-ttu-id="dc561-544">Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.</span><span class="sxs-lookup"><span data-stu-id="dc561-544">Similarly, refresh tokens shouldn't be issued to a client that isn't trusted, as doing so gives the client unlimited access unless other restrictions are put into place.</span></span>

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a><span data-ttu-id="dc561-545">Отправка вызовов API с API клиента на API сервера для вызова сторонних API</span><span class="sxs-lookup"><span data-stu-id="dc561-545">Make API calls from the client to the server API in order to call third-party APIs</span></span>

<span data-ttu-id="dc561-546">Отправьте вызов API с API клиента на API сервера.</span><span class="sxs-lookup"><span data-stu-id="dc561-546">Make an API call from the client to the server API.</span></span> <span data-ttu-id="dc561-547">На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.</span><span class="sxs-lookup"><span data-stu-id="dc561-547">From the server, retrieve the access token for the third-party API resource and issue whatever call is necessary.</span></span>

<span data-ttu-id="dc561-548">Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.</span><span class="sxs-lookup"><span data-stu-id="dc561-548">While this approach requires an extra network hop through the server to call a third-party API, it ultimately results in a safer experience:</span></span>

* <span data-ttu-id="dc561-549">Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.</span><span class="sxs-lookup"><span data-stu-id="dc561-549">The server can store refresh tokens and ensure that the app doesn't lose access to third-party resources.</span></span>
* <span data-ttu-id="dc561-550">Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.</span><span class="sxs-lookup"><span data-stu-id="dc561-550">The app can't leak access tokens from the server that might contain more sensitive permissions.</span></span>

## <a name="use-open-id-connect-oidc-v20-endpoints"></a><span data-ttu-id="dc561-551">Использование конечных точек Open ID Connect (OIDC) версии 2.0</span><span class="sxs-lookup"><span data-stu-id="dc561-551">Use Open ID Connect (OIDC) v2.0 endpoints</span></span>

<span data-ttu-id="dc561-552">Библиотека проверки подлинности и Blazor Шаблоны используют конечные точки Open ID Connect (OIDC) v 1.0.</span><span class="sxs-lookup"><span data-stu-id="dc561-552">The authentication library and Blazor templates use Open ID Connect (OIDC) v1.0 endpoints.</span></span> <span data-ttu-id="dc561-553">Чтобы использовать конечную точку версии 2.0, настройте параметр носителя JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> .</span><span class="sxs-lookup"><span data-stu-id="dc561-553">To use a v2.0 endpoint, configure the JWT Bearer <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> option.</span></span> <span data-ttu-id="dc561-554">В следующем примере AAD настраивается для версии 2.0 путем добавления `v2.0` сегмента к <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> свойству:</span><span class="sxs-lookup"><span data-stu-id="dc561-554">In the following example, AAD is configured for v2.0 by appending a `v2.0` segment to the <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> property:</span></span>

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

<span data-ttu-id="dc561-555">Кроме того, параметр можно задать в файле параметров приложения (*appSettings. JSON*):</span><span class="sxs-lookup"><span data-stu-id="dc561-555">Alternatively, the setting can be made in the app settings (*appsettings.json*) file:</span></span>

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

<span data-ttu-id="dc561-556">Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например с поставщиками, не являющимися владельцами AAD, задайте <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> свойство напрямую.</span><span class="sxs-lookup"><span data-stu-id="dc561-556">If tacking on a segment to the authority isn't appropriate for the app's OIDC provider, such as with non-AAD providers, set the <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> property directly.</span></span> <span data-ttu-id="dc561-557">Либо задайте свойство в <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> или в файле параметров приложения (*appSettings. JSON*) с помощью `Authority` ключа.</span><span class="sxs-lookup"><span data-stu-id="dc561-557">Either set the property in <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> or in the app settings file (*appsettings.json*) with the `Authority` key.</span></span>

<span data-ttu-id="dc561-558">Список утверждений в токене идентификатора изменяется для конечных точек версии 2.0.</span><span class="sxs-lookup"><span data-stu-id="dc561-558">The list of claims in the ID token changes for v2.0 endpoints.</span></span> <span data-ttu-id="dc561-559">Дополнительные сведения см. [в статье Почему обновление платформы Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span><span class="sxs-lookup"><span data-stu-id="dc561-559">For more information, see [Why update to Microsoft identity platform (v2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).</span></span>
