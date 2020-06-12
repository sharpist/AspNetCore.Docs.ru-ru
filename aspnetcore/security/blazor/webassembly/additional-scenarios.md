---
title: ASP.NET Core Blazor дополнительных сценариев безопасности для сборки
author: guardrex
description: Узнайте, как настроить Blazor сборку для дополнительных сценариев безопасности.
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
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 35038cb7b96afd7c009f1210251e38273aa4aad8
ms.sourcegitcommit: 6371114344a5f4fbc5d4a119b0be1ad3762e0216
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84679661"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor дополнительных сценариев безопасности для сборки

[Хавьер Калварро Воронков](https://github.com/javiercn) и [Люк ЛаСаМ](https://github.com/guardrex)

## <a name="attach-tokens-to-outgoing-requests"></a>Присоединение маркеров к исходящим запросам

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler>Службу можно использовать с <xref:System.Net.Http.HttpClient> для присоединения маркеров доступа к исходящим запросам. Токены получаются с помощью существующей <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider> службы. Если токен не может быть получен, <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> создается исключение. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>содержит <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> метод, который можно использовать для перехода пользователя к поставщику удостоверений для получения нового маркера. Для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настройки можно использовать полномочные URL-адреса, области и URL-адрес возврата с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> метода.

Используйте один из следующих подходов для настройки обработчика сообщений для исходящих запросов:

* [Пользовательский класс аусоризатионмессажехандлер](#custom-authorizationmessagehandler-class) (*рекомендуется*)
* [Настройка Аусоризатионмессажехандлер](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a>Пользовательский класс Аусоризатионмессажехандлер

В следующем примере расширяется пользовательский класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> , который можно использовать для настройки <xref:System.Net.Http.HttpClient> :

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

В `Program.Main` (*Program.CS*) <xref:System.Net.Http.HttpClient> Настройка настроена с помощью пользовательского обработчика сообщений авторизации:

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Настроенный параметр <xref:System.Net.Http.HttpClient> используется для выполнения запросов с помощью шаблона [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) . Когда клиент создается с помощью <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) Package), <xref:System.Net.Http.HttpClient> передаются экземпляры, которые включают маркеры доступа при выполнении запросов к API сервера:

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

### <a name="configure-authorizationmessagehandler"></a>Настройка Аусоризатионмессажехандлер

В следующем примере <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настраивается <xref:System.Net.Http.HttpClient> в `Program.Main` (*Program.CS*):

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

Для удобства <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> включается предварительно настроенный базовый адрес приложения в качестве разрешенного URL-адреса. Шаблоны веб-сборок с поддержкой проверки подлинности Blazor теперь используют <xref:System.Net.Http.IHttpClientFactory> (пакет[Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) ) в проекте API сервера для настройки <xref:System.Net.Http.HttpClient> с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> :

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

При создании клиента с помощью <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> в предыдущем примере <xref:System.Net.Http.HttpClient> передаются экземпляры, которые включают маркеры доступа при выполнении запросов к серверному проекту.

Настроенный параметр <xref:System.Net.Http.HttpClient> используется для выполнения запросов с помощью шаблона [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) :

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

## <a name="typed-httpclient"></a>Типизированный класс HttpClient

Можно определить типизированный клиент, который обрабатывает все проблемы получения HTTP и маркеров в рамках одного класса.

*WeatherForecastClient.cs*:

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

`Program.Main` (*Program.cs*):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

Компонент `FetchData` (*Pages/FetchData.razor*):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Настройка обработчика HttpClient

Обработчик можно дополнительно настроить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.

`Program.Main` (*Program.cs*):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Запросы, не прошедшие проверку подлинности или неавторизованные веб-API в приложении с защищенным клиентом по умолчанию

Если приложение веб- Blazor сборки обычно использует безопасное значение по умолчанию <xref:System.Net.Http.HttpClient> , оно также может выполнять непроверенные или неавторизованные запросы веб-API, настроив именованную <xref:System.Net.Http.HttpClient> :

`Program.Main` (*Program.cs*):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Приведенная выше регистрация является дополнением к существующей безопасной регистрации по умолчанию <xref:System.Net.Http.HttpClient> .

Компонент создает <xref:System.Net.Http.HttpClient> из <xref:System.Net.Http.IHttpClientFactory> ([Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) -пакет) для создания непроверенных или неавторизованных запросов:

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
> Контроллер в API сервера `WeatherForecastNoAuthenticationController` для предыдущего примера не помечен [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) атрибутом.

## <a name="request-additional-access-tokens"></a>Запрос дополнительных маркеров доступа

Маркеры доступа могут быть получены вручную путем вызова `IAccessTokenProvider.RequestAccessToken` .

В следующем примере дополнительные Azure Active Directory (AAD) Microsoft Graph области API необходимы приложениям для чтения данных пользователей и отправки почты. После добавления разрешений Microsoft Graph API на портале Azure AAD дополнительные области настраиваются в клиентском приложении.

`Program.Main` (*Program.cs*):

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

`IAccessTokenProvider.RequestToken`Метод предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с заданным набором областей.

В Razor компоненте:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType>возврата

* `true`с помощью `token` для использования.
* `false`значение, если маркер не извлекается.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient и HttpRequestMessage с параметрами запроса API FETCH

При запуске в сборке в Blazor приложении [HttpClient](xref:fundamentals/http-requests) и <xref:System.Net.Http.HttpRequestMessage> может использоваться для настройки запросов. Например, можно указать метод HTTP и заголовки запроса. Следующий компонент выполняет запрос к `POST` конечной точке API To Do List на сервере и отображает текст ответа:

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

Реализация <xref:System.Net.Http.HttpClient> [виндоворворкерглобалскопе. fetch ()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch)в .NET-сборке использует. Выборка позволяет настроить несколько [параметров для конкретного запроса](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Параметры запроса HTTP FETCH можно настроить с помощью <xref:System.Net.Http.HttpRequestMessage> методов расширения, приведенных в следующей таблице.

| Метод расширения | Получение свойства запроса |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [credentials](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [Мбайта](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [mode](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [контроля](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Можно задать дополнительные параметры с помощью более универсального <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A> метода расширения.
 
HTTP-ответ обычно замещается в буфер в Blazor приложении сборки, чтобы обеспечить поддержку синхронных операций чтения содержимого ответа. Чтобы включить поддержку потоковой передачи ответов, используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> метод расширения для запроса.

Чтобы включить учетные данные в запрос между источниками, используйте <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> метод расширения:

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Дополнительные сведения о возможностях API-получения см. в разделе [MDN Web документация: виндоворворкерглобалскопе. fetch ():P араметерс](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS этот `Authorization` заголовок должен быть разрешен ПОЛИТИКОЙ CORS.

Следующая политика включает в себя настройку для:

* Источники запроса ( `http://localhost:5000` , `https://localhost:5001` ).
* Любой метод (глагол).
* `Content-Type`и `Authorization` заголовки. Чтобы разрешить пользовательский заголовок (например, `x-custom-header` ), выведите список заголовков при вызове метода <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> .
* Учетные данные, которые задаются кодом JavaScript на стороне клиента ( `credentials` свойство имеет значение `include` ).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Дополнительные сведения см <xref:security/cors> . в разделе и компоненте тестера HTTP-запросов примера приложения (*Components/хттпрекуесттестер. Razor*).

## <a name="handle-token-request-errors"></a>Обработку ошибок запросов маркера

Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в рамках SPA и в Identity поставщике (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.

Маркеры, которые IP-адрес выдает пользователю, обычно действительны в течение короткого периода времени, примерно один час, поэтому клиентское приложение должно регулярно получать новые токены. В противном случае пользователь будет зарегистрирован после истечения срока действия назначенных токенов. В большинстве случаев клиенты OIDC могут подготавливать новые маркеры, не требуя от пользователя повторной проверки подлинности благодаря состоянию аутентификации или сеансу, который хранится в IP-адресе.

В некоторых случаях клиент не может получить маркер без взаимодействия с пользователем, например, когда по какой-либо причине пользователь явно выходит из IP. Эта ситуация возникает при посещении `https://login.microsoftonline.com` и выходе пользователя из систему. В этих сценариях приложение не знает о немедленном выходе пользователя из систему. Любой токен, который удерживает клиент, может быть недействительным. Кроме того, клиент не может подготавливать новый маркер без взаимодействия с пользователем после истечения срока действия текущего маркера.

Эти сценарии не относятся к проверке подлинности на основе маркеров. Они являются частью природы одностраничные приложения. Проверка подлинности с помощью файлов cookie также не вызывает API сервера, если удаляется файл cookie для аутентификации.

Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:

* Чтобы создать новый маркер доступа для вызова API, пользователю может потребоваться выполнить повторную проверку подлинности.
* Даже если у клиента есть токен, который кажется допустимым, вызов сервера может завершиться ошибкой, так как маркер был отозван пользователем.

Когда приложение запрашивает маркер, существует два возможных результата:

* Запрос выполнен успешно, и приложение имеет действительный токен.
* Запрос завершается ошибкой, и приложение должно повторно пройти проверку подлинности пользователя, чтобы получить новый маркер.

При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления. Существует несколько подходов, повышающих уровень сложности:

* Сохранение текущего состояния страницы в хранилище сеансов. Во время [события жизненного цикла онинитиализедасинк](xref:blazor/lifecycle#component-initialization-methods) ( <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> ) Проверьте, можно ли восстановить состояние, прежде чем продолжить.
* Добавьте параметр строки запроса и используйте его в качестве способа сигнализации приложению о необходимости повторного расконсервации ранее сохраненного состояния.
* Добавьте параметр строки запроса с уникальным идентификатором для хранения данных в хранилище сеанса без риска конфликтов с другими элементами.

В приведенном ниже примере показано, как выполнить следующие задачи.

* Сохраните состояние перед перенаправлением на страницу входа.
* Восстановите предыдущее состояние после проверки подлинности с помощью параметра строки запроса.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Сохранить состояние приложения перед операцией проверки подлинности

Во время операции аутентификации существуют случаи, когда необходимо сохранить состояние приложения, прежде чем браузер перенаправится на IP-адрес. Это может быть так, когда вы используете контейнер состояния и хотите восстановить состояние после завершения проверки подлинности. Можно использовать пользовательский объект состояния проверки подлинности для сохранения состояния приложения или ссылки на него и восстановления этого состояния после успешного завершения операции проверки подлинности. В следующем примере демонстрируется подход.

Класс контейнера состояния создается в приложении со свойствами для хранения значений состояния приложения. В следующем примере контейнер используется для сохранения значения счетчика компонента шаблона по умолчанию `Counter` (*pages/Counter. Razor*). Методы сериализации и десериализации контейнера основаны на <xref:System.Text.Json> .

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

`Counter`Компонент использует контейнер состояния для сохранения `currentCount` значения за пределами компонента:

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

Создайте `ApplicationAuthenticationState` из <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState> . Укажите `Id` свойство, которое служит в качестве идентификатора для локально сохраненного состояния.

*ApplicationAuthenticationState.CS*:

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

`Authentication`Компонент (*pages/Authentication. Razor*) сохраняет и восстанавливает состояние приложения с помощью локального хранилища сеансов с `StateContainer` методами сериализации и десериализации, `GetStateForLocalStorage` а также `SetStateFromLocalStorage` :

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

В этом примере для проверки подлинности используется Azure Active Directory (AAD). В `Program.Main` (*Program.CS*):

* Параметр `ApplicationAuthenticationState` настроен в качестве типа Microsoft сквозной Library (MSAL) `RemoteAuthenticationState` .
* Контейнер состояния регистрируется в контейнере службы.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Настройка маршрутов приложений

По умолчанию библиотека [Microsoft. AspNetCore. Components. веб-сборка. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) использует маршруты, приведенные в следующей таблице, для представления различных состояний проверки подлинности.

| Маршрут                            | Назначение |
| -------------------------------- | ------- |
| `authentication/login`           | Активирует операцию входа. |
| `authentication/login-callback`  | Обрабатывает результат любой операции входа. |
| `authentication/login-failed`    | Отображает сообщения об ошибках при сбое операции входа по какой-либо причине. |
| `authentication/logout`          | Активирует операцию выхода. |
| `authentication/logout-callback` | Обрабатывает результат операции выхода. |
| `authentication/logout-failed`   | Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине. |
| `authentication/logged-out`      | Указывает, что пользователь успешно выполнил выход. |
| `authentication/profile`         | Активирует операцию для изменения профиля пользователя. |
| `authentication/register`        | Активирует операцию для регистрации нового пользователя. |

Маршруты, показанные в предыдущей таблице, можно настроить с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType> . При настройке параметров для предоставления настраиваемых маршрутов убедитесь, что у приложения есть маршрут, который обрабатывает каждый путь.

В следующем примере все пути имеют префикс `/security` .

`Authentication`Component (*pages/Authentication. Razor*):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (*Program.cs*):

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

Если требование вызывается для совершенно разных путей, задайте маршруты, как описано выше, и выполните визуализацию <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> с помощью явного параметра Action:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

При необходимости вы можете разбить пользовательский интерфейс на разные страницы.

## <a name="customize-the-authentication-user-interface"></a>Настройка пользовательского интерфейса проверки подлинности

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>включает набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности. Каждое состояние можно настроить путем передачи пользовательского <xref:Microsoft.AspNetCore.Components.RenderFragment> . Для настройки отображаемого текста во время первоначального входа в систему может измениться <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> следующим образом.

`Authentication`Component (*pages/Authentication. Razor*):

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

В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> имеется один фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в следующей таблице.

| Маршрут                            | Fragment (Фрагмент)                |
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

## <a name="customize-the-user"></a>Настройка пользователя

Пользователи, привязанные к приложению, могут быть настроены. В следующем примере все пользователи, прошедшие проверку подлинности, получают `amr` утверждение для каждого из методов проверки подлинности пользователя.

Создайте класс, расширяющий <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount> класс:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Создайте фабрику, которая расширяет <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601> :

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

Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности. Любая из следующих регистраций допустима: 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>:

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>:

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>:

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

## <a name="support-prerendering-with-authentication"></a>Поддержка предварительной отрисовки с помощью проверки подлинности

Выполнив инструкции в одном из разделов, посвященных размещенным приложениям Blazor WebAssembly, выполните приведенные далее действия, чтобы создать приложение, которое:

* предварительно отрисовывает пути, не требующие авторизации;
* не выполняет предварительную отрисовку путей, требующих авторизации.

В классе `Program` клиентского приложения (*Program.cs*) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):

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

В `Startup.ConfigureServices` серверного приложения зарегистрируйте следующие дополнительные службы:

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

В методе серверного приложения `Startup.Configure` замените [конечные точки. Мапфаллбакктофиле ("index.html")](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) с [конечными точками. Мапфаллбакктопаже ("/_Host")](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

В серверном приложении создайте папку *Pages*, если она отсутствует. В папке *Pages* серверного приложения создайте страницу *_Host.cshtml*. Вставьте содержимое из файла *wwwroot/index.html* клиентского приложения в файл *Pages/_Host.cshtml*. Обновите содержимое файла:

* Добавьте `@page "_Host"` в начало файла.
* Замените тег `<app>Loading...</app>` следующим:

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
  
## <a name="options-for-hosted-apps-and-third-party-login-providers"></a>Варианты для размещенных приложений и сторонних поставщиков входа

При проверке подлинности и авторизации размещенного приложения Blazor WebAssembly в стороннем поставщике доступно несколько вариантов проверки подлинности пользователя. Выбор варианта зависит от вашего сценария.

Для получения дополнительной информации см. <xref:security/authentication/social/additional-claims>.

### <a name="authenticate-users-to-only-call-protected-third-party-apis"></a>Проверка подлинности пользователей для вызова только защищенных сторонних API

Проверьте подлинность пользователя с помощью потока OAuth на стороне клиента в стороннем поставщике API:

 ```csharp
 builder.services.AddOidcAuthentication(options => { ... });
 ```
 
 В этом сценарии:

* Сервер, на котором размещено приложение, не имеет особого значения.
* Невозможно обеспечить защиту API на сервере.
* Приложение может вызывать только защищенные сторонние интерфейсы API.

### <a name="authenticate-users-with-a-third-party-provider-and-call-protected-apis-on-the-host-server-and-the-third-party"></a>Проверка подлинности пользователей в стороннем поставщике и вызов защищенных API на сервере узла и сторонних API

Настройка Identity со сторонним поставщиком входа. Получите токены, необходимые для доступа к сторонним API, и сохраните их.

При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности. На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Использование токена доступа сервера для получения стороннего токена доступа

С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера. В нем используйте сторонний маркер доступа, чтобы вызывать сторонние ресурсы API непосредственно из Identity клиента.

Этот вариант использовать не рекомендуется. Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента. С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента. Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.

* Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.
* Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Отправка вызовов API с API клиента на API сервера для вызова сторонних API

Отправьте вызов API с API клиента на API сервера. На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.

Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.

* Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.
* Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Использование конечных точек Open ID Connect (OIDC) версии 2.0

Библиотека проверки подлинности и Blazor Шаблоны используют конечные точки Open ID Connect (OIDC) v 1.0. Чтобы использовать конечную точку версии 2.0, настройте параметр носителя JWT <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> . В следующем примере AAD настраивается для версии 2.0 путем добавления `v2.0` сегмента к <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority> свойству:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Кроме того, параметр можно задать в файле параметров приложения (*appsettings.json*):

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например с поставщиками, не являющимися владельцами AAD, задайте <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> свойство напрямую. Либо задайте свойство в <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions> или в файле параметров приложения (*appsettings.jsв*) с помощью `Authority` ключа.

Список утверждений в токене идентификатора изменяется для конечных точек версии 2.0. Дополнительные сведения см. [в статье Почему обновление платформы Microsoft Identity Platform (v 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison).
