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
ms.openlocfilehash: 13007df4ddddd31dd0508e9526775a6d33e0fd97
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242918"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>Сценарии обеспечения дополнительной безопасности ASP.NET Core Blazor WebAssembly

Авторы: [Хавьер Кальварро Нельсон](https://github.com/javiercn) (Javier Calvarro Nelson) и [Люк Латэм](https://github.com/guardrex) (Luke Latham)

## <a name="attach-tokens-to-outgoing-requests"></a>Присоединение маркеров к исходящим запросам

Службу <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> можно использовать с <xref:System.Net.Http.HttpClient> для присоединения маркеров доступа к исходящим запросам. Маркеры получаются с помощью существующей службы <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider>. Если маркер получить невозможно, создается исключение <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>. В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> есть <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException.Redirect%2A> метод, с помощью которого можно направить пользователя к поставщику удостоверений для получения нового маркера. Для <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> можно настроить разрешенные URL-адреса, области и URL-адреса возврата с помощью метода <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A>.

Чтобы настроить обработчик сообщений для исходящих запросов, используйте один из следующих подходов:

* [Пользовательский класс `AuthorizationMessageHandler`](#custom-authorizationmessagehandler-class) (*рекомендуется*)
* [Настройка `AuthorizationMessageHandler`](#configure-authorizationmessagehandler)

### <a name="custom-authorizationmessagehandler-class"></a>Пользовательский класс AuthorizationMessageHandler

В следующем примере класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> расширяется пользовательским классом, который можно использовать для настройки <xref:System.Net.Http.HttpClient>:

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

В `Program.Main` (`Program.cs`) объект <xref:System.Net.Http.HttpClient> настраивается с помощью пользовательского обработчика сообщений авторизации:

```csharp
builder.Services.AddTransient<CustomAuthorizationMessageHandler>();

builder.Services.AddHttpClient("ServerAPI",
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<CustomAuthorizationMessageHandler>();
```

Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch). Когда клиент создается с помощью метода <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)), при выполнении запросов к интерфейсу API сервера объекту <xref:System.Net.Http.HttpClient> передаются экземпляры, содержащие маркеры доступа:

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

### <a name="configure-authorizationmessagehandler"></a>Configure AuthorizationMessageHandler

В следующем примере обработчик <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler> настраивает объект <xref:System.Net.Http.HttpClient> в `Program.Main` (`Program.cs`):

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

Для удобства включен обработчик <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler> с базовым адресом приложения, предварительно настроенным в качестве авторизованного URL-адреса. В шаблонах Blazor WebAssembly с поддержкой проверки подлинности теперь используется интерфейс <xref:System.Net.Http.IHttpClientFactory> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)) в проекте API сервера для настройки объекта <xref:System.Net.Http.HttpClient> с <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.BaseAddressAuthorizationMessageHandler>:

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

Когда в предыдущем примере клиент создается с помощью метода <xref:System.Net.Http.IHttpClientFactory.CreateClient%2A>, при выполнении запросов к проекту сервера объекту <xref:System.Net.Http.HttpClient> передаются экземпляры, содержащие маркеры доступа.

Настроенный объект <xref:System.Net.Http.HttpClient> используется для выполнения авторизованных запросов с помощью шаблона [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch):

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

Можно определить типизированный клиент, который будет обрабатывать все аспекты, связанные с HTTP и получением маркеров, в пределах одного класса.

`WeatherForecastClient.cs`.

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

Заполнитель `{APP ASSEMBLY}` — это имя сборки приложения (например, `using static BlazorSample.Data;`).

`Program.Main` (`Program.cs`):

```csharp
using System.Net.Http;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

...

builder.Services.AddHttpClient<WeatherForecastClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

Компонент `FetchData` (`Pages/FetchData.razor`):

```razor
@inject WeatherForecastClient Client

...

protected override async Task OnInitializedAsync()
{
    forecasts = await Client.GetForecastAsync();
}
```

## <a name="configure-the-httpclient-handler"></a>Настройка обработчика HttpClient

Обработчик можно дополнительно настроить с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AuthorizationMessageHandler.ConfigureHandler%2A> для исходящих HTTP-запросов.

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient<WeatherForecastClient>(client => client.BaseAddress = new Uri("https://www.example.com/base"))
    .AddHttpMessageHandler(sp => sp.GetRequiredService<AuthorizationMessageHandler>()
    .ConfigureHandler(new [] { "https://www.example.com/base" },
        scopes: new[] { "example.read", "example.write" }));
```

## <a name="unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client"></a>Запросы веб-API, не прошедшие проверку подлинности или неавторизованные, в приложении с защищенным клиентом по умолчанию

Если приложение Blazor WebAssembly обычно использует защищенный клиент <xref:System.Net.Http.HttpClient> по умолчанию, приложение также может выполнять неавторизованные или не прошедшие проверку запросы к веб-API путем настройки именованного клиента <xref:System.Net.Http.HttpClient>:

`Program.Main` (`Program.cs`):

```csharp
builder.Services.AddHttpClient("ServerAPI.NoAuthenticationClient", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress));
```

Приведенная выше регистрация является дополнением к существующей регистрации защищенного клиента <xref:System.Net.Http.HttpClient> по умолчанию.

Компонент создает клиент <xref:System.Net.Http.HttpClient> на основе <xref:System.Net.Http.IHttpClientFactory> (из пакета [`Microsoft.Extensions.Http`](https://www.nuget.org/packages/Microsoft.Extensions.Http/)) для выполнения не прошедших проверку или неавторизованных запросов:

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
> Контроллер в интерфейсе API сервера (`WeatherForecastNoAuthenticationController` в предыдущем примере) не помечен атрибутом [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute).

## <a name="request-additional-access-tokens"></a>Запрос дополнительных маркеров доступа

Маркеры доступа можно получать вручную, вызывая `IAccessTokenProvider.RequestAccessToken`.

В приведенном ниже примере для чтения данных пользователей и отправки почты приложениям требуются дополнительные области API Microsoft Graph Azure Active Directory (AAD). После добавления разрешений API Microsoft Graph на портале Azure AAD дополнительные области настраиваются в клиентском приложении.

`Program.Main` (`Program.cs`):

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

Метод `IAccessTokenProvider.RequestToken` предоставляет перегрузку, которая позволяет приложению подготавливать маркер доступа с указанным набором областей.

В компоненте Razor:

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

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A?displayProperty=nameWithType> возвращает:

* `true` с пригодным для использования `token`.
* `false`, если маркер не получен.

## <a name="httpclient-and-httprequestmessage-with-fetch-api-request-options"></a>HttpClient и HttpRequestMessage с параметрами запроса Fetch API

При выполнении в WebAssembly в приложении Blazor WebAssembly для настройки запросов можно использовать [`HttpClient`](xref:fundamentals/http-requests) и <xref:System.Net.Http.HttpRequestMessage>. Например, можно указать метод HTTP и заголовки запроса. Следующий компонент выполняет запрос `POST` к конечной точке API списка дел на сервере и отображает текст ответа:

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

Реализация .NET WebAssembly <xref:System.Net.Http.HttpClient> использует [WindowOrWorkerGlobalScope.fetch()](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch). Извлечение позволяет настроить несколько [параметров, связанных с запросами](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters). 

Параметры запроса HTTP на извлечение можно настроить с помощью методов расширения <xref:System.Net.Http.HttpRequestMessage>, приведенных в следующей таблице.

| Метод расширения | Свойство запроса на извлечение |
| --- | --- |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A> | [`credentials`](https://developer.mozilla.org/docs/Web/API/Request/credentials) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCache%2A> | [`cache`](https://developer.mozilla.org/docs/Web/API/Request/cache) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestMode%2A> | [`mode`](https://developer.mozilla.org/docs/Web/API/Request/mode) |
| <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestIntegrity%2A> | [`integrity`](https://developer.mozilla.org/docs/Web/API/Request/integrity) |

Вы можете задать дополнительные параметры с помощью более универсального метода расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestOption%2A>.
 
HTTP-ответ обычно помещается в буфер в приложении Blazor WebAssembly для выполнения операций синхронизации содержимого ответа. Для выполнения потоковой передачи ответов используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserResponseStreamingEnabled%2A> для запроса.

Чтобы включить учетные данные в запрос независимо от источника, используйте метод расширения <xref:Microsoft.AspNetCore.Components.WebAssembly.Http.WebAssemblyHttpRequestMessageExtensions.SetBrowserRequestCredentials%2A>.

```csharp
requestMessage.SetBrowserRequestCredentials(BrowserRequestCredentials.Include);
```

Дополнительные сведения о возможностях Fetch API см. в разделе [Веб-документы MDN: WindowOrWorkerGlobalScope.fetch():Parameters](https://developer.mozilla.org/docs/Web/API/WindowOrWorkerGlobalScope/fetch#Parameters).

При отправке учетных данных (файлов cookie или заголовков авторизации) в запросах CORS заголовок `Authorization` должен быть разрешен политикой CORS.

Следующая политика включает в себя настройку для следующих элементов:

* Источники запроса (`http://localhost:5000`, `https://localhost:5001`).
* Любой метод (команда).
* Заголовки `Content-Type` и `Authorization`. Чтобы разрешить пользовательский заголовок (например, `x-custom-header`), выведите список заголовков при вызове <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>.
* Учетные данные, установленные клиентским кодом JavaScript (для свойства `credentials` задано значение `include`).

```csharp
app.UseCors(policy => 
    policy.WithOrigins("http://localhost:5000", "https://localhost:5001")
    .AllowAnyMethod()
    .WithHeaders(HeaderNames.ContentType, HeaderNames.Authorization, "x-custom-header")
    .AllowCredentials());
```

Дополнительные сведения см. в разделе <xref:security/cors> и компоненте "Тестер HTTP-запросов" примера приложения (`Components/HTTPRequestTester.razor`).

## <a name="handle-token-request-errors"></a>Обработка ошибок при выполнении запросов маркеров

Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в приложении SPA и в поставщике Identity (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.

Маркеры, которые IP-адрес выдает для пользователя, обычно действительны недолго (как правило, около часа), поэтому клиентское приложение должно регулярно получать новые маркеры. В противном случае после истечения срока действия предоставленных маркеров будет выполнен выход пользователя из системы. В большинстве случаев клиенты OIDC могут подготавливать новые маркеры без повторного прохождения проверки подлинности пользователем благодаря сохранению состояния проверки подлинности или сеанса в поставщике удостоверений.

В некоторых случаях клиент не может получить маркер без участия пользователя, например, если по какой-либо причине пользователь явно вышел из поставщика удостоверений. Такая ситуация возникает, если пользователь переходит по адресу `https://login.microsoftonline.com` и выполняет выход из системы. В этих случаях приложению не становится известно о выходе пользователя из системы немедленно. Любой маркер, который имеется у клиента, может быть уже недействительным. Кроме того, клиент не может подготовить новый маркер без участия пользователя после истечения срока действия текущего маркера.

Такие сценарии нехарактерны для проверки подлинности на основе маркеров. Они проистекают из особенностей одностраничных приложений. Одностраничному приложению, использующему файлы cookie, также не удастся вызвать интерфейс API сервера в случае удаления файла cookie для проверки подлинности.

Когда приложение выполняет вызовы API к защищенным ресурсам, необходимо учитывать следующее:

* Для подготовки нового маркера доступа с целью вызова API пользователю может потребоваться пройти проверку подлинности повторно.
* Даже если у клиента есть маркер, который представляется действительным, вызов сервера может завершиться ошибкой из-за того, что маркер был отозван пользователем.

Когда приложение запрашивает маркер, возможны два результата:

* Запрос выполняется успешно, и приложение получает действительный маркер.
* Запрос завершается ошибкой, и приложение должно повторно провести проверку подлинности пользователя, чтобы получить новый маркер.

При сбое запроса маркера необходимо решить, следует ли сохранить текущее состояние перед выполнением перенаправления. Ниже приведены возможные подходы в порядке увеличения сложности.

* Сохраните текущее состояние страницы в хранилище сеансов. Во время [события жизненного цикла `OnInitializedAsync`](xref:blazor/components/lifecycle#component-initialization-methods) (<xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>) проверьте, можно ли восстановить состояние, прежде чем продолжить.
* Добавьте параметр строки запроса и используйте его, чтобы сообщить приложению о необходимости восстановить ранее сохраненное состояние.
* Добавьте параметр строки запроса с уникальным идентификатором для сохранения данных в хранилище сеансов без риска конфликтов с другими элементами.

В приведенном ниже примере показано, как выполнить следующие задачи.

* сохранить состояние перед перенаправлением на страницу входа;
* восстановить предыдущее состояние после проверки подлинности с помощью параметра строки запроса.

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

## <a name="save-app-state-before-an-authentication-operation"></a>Сохранение состояния приложения перед операцией проверки подлинности

Во время операции проверки подлинности существуют случаи, когда необходимо сохранить состояние приложения, прежде чем в браузере будет выполнено перенаправление в поставщик удостоверений. Например, такая потребность может возникнуть, когда вы используете контейнер состояния и хотите восстановить состояние после успешной проверки подлинности. Вы можете использовать пользовательский объект состояния проверки подлинности для сохранения состояния приложения или ссылки на него и восстанавливать это состояние после успешного завершения проверки подлинности. Такой подход демонстрируется в приведенном ниже примере.

В приложении создается класс контейнера состояния со свойствами для хранения значений состояния приложения. В приведенном ниже примере контейнер используется для хранения значения счетчика для компонента `Counter` шаблона по умолчанию (`Pages/Counter.razor`). Методы сериализации и десериализации контейнера основаны на <xref:System.Text.Json>.

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

Компонент `Counter` использует контейнер состояния для хранения значения `currentCount` за пределами компонента:

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

Создайте `ApplicationAuthenticationState` на основе <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationState>. Укажите свойство `Id`, которое служит идентификатором для локально хранящегося состояния.

`ApplicationAuthenticationState.cs`.

```csharp
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class ApplicationAuthenticationState : RemoteAuthenticationState
{
    public string Id { get; set; }
}
```

Компонент `Authentication` (`Pages/Authentication.razor`) сохраняет и восстанавливает состояние приложения, используя локальное хранилище сеансов, с помощью методов сериализации и десериализации `StateContainer` (`GetStateForLocalStorage` и `SetStateFromLocalStorage`):

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

В этом примере для проверки подлинности используется Azure Active Directory (AAD). В `Program.Main` (`Program.cs`):

* `ApplicationAuthenticationState` настраивается с типом `RemoteAuthenticationState` из библиотеки проверки подлинности Майкрософт (MSAL).
* Контейнер состояния регистрируется в контейнере службы.

```csharp
builder.Services.AddMsalAuthentication<ApplicationAuthenticationState>(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});

builder.Services.AddSingleton<StateContainer>();
```

## <a name="customize-app-routes"></a>Настройка маршрутов приложения

Для представления различных состояний проверки подлинности библиотека [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) по умолчанию использует маршруты, приведенные в таблице ниже.

| Маршрут                            | Цель |
| -------------------------------- | ------- |
| `authentication/login`           | Активирует операцию входа. |
| `authentication/login-callback`  | Обрабатывает результат любой операции входа. |
| `authentication/login-failed`    | Отображает сообщения об ошибках при сбое операции входа по какой-либо причине. |
| `authentication/logout`          | Активирует операцию выхода. |
| `authentication/logout-callback` | Обрабатывает результат любой операции выхода. |
| `authentication/logout-failed`   | Отображает сообщения об ошибках при сбое операции выхода по какой-либо причине. |
| `authentication/logged-out`      | Указывает, что пользователь успешно выполнил выход. |
| `authentication/profile`         | Активирует операцию изменения профиля пользователя. |
| `authentication/register`        | Активирует операцию регистрации нового пользователя. |

Маршруты, представленные в предыдущей таблице, можно настраивать с помощью <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticationOptions%601.AuthenticationPaths%2A?displayProperty=nameWithType>. При настройке параметров для предоставления пользовательских маршрутов убедитесь в том, что у приложения есть маршрут для обработки каждого пути.

В приведенном ниже примере все пути имеют префикс `/security`.

Компонент `Authentication` (`Pages/Authentication.razor`):

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main` (`Program.cs`):

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

Если требуются совершенно разные пути, задайте маршруты, как описано выше, и обработайте <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> с помощью явного параметра действия:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

При необходимости можно разделить пользовательский интерфейс на несколько страниц.

## <a name="customize-the-authentication-user-interface"></a>Настройка пользовательского интерфейса проверки подлинности

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> содержит набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности. Каждое состояние можно настроить, передав пользовательский объект <xref:Microsoft.AspNetCore.Components.RenderFragment>. Чтобы настроить текст, отображаемый во время первоначального входа в систему, можно изменить <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> указанным ниже образом.

Компонент `Authentication` (`Pages/Authentication.razor`):

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

В <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView> есть фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в приведенной ниже таблице.

| Маршрут                            | Fragment                |
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

Пользователей, привязанных к приложению, можно настраивать. В приведенном ниже примере все пользователи, прошедшие проверку подлинности, получают утверждение `amr` для каждого из методов проверки подлинности.

Создайте класс, расширяющий класс <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteUserAccount>:

```csharp
using System.Text.Json.Serialization;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class CustomUserAccount : RemoteUserAccount
{
    [JsonPropertyName("amr")]
    public string[] AuthenticationMethod { get; set; }
}
```

Создайте фабрику, расширяющую <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccountClaimsPrincipalFactory%601>:

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

Зарегистрируйте `CustomAccountFactory` для используемого поставщика проверки подлинности. Допустима любая из следующих регистраций: 

* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>.

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

* <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>.

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
  
* <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddApiAuthorization%2A>.

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

В классе `Program` клиентского приложения (`Program.cs`) включите регистрации общих служб в отдельный метод (например, `ConfigureCommonServices`):

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

В методе `Startup.Configure` серверного приложения замените [`endpoints.MapFallbackToFile("index.html")`](xref:Microsoft.AspNetCore.Builder.StaticFilesEndpointRouteBuilderExtensions.MapFallbackToFile%2A) на [`endpoints.MapFallbackToPage("/_Host")`](xref:Microsoft.AspNetCore.Builder.RazorPagesEndpointRouteBuilderExtensions.MapFallbackToPage%2A):

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapFallbackToPage("/_Host");
});
```

В серверном приложении создайте папку `Pages`, если она отсутствует. В папке серверного приложения `Pages` создайте страницу `_Host.cshtml`. Вставьте содержимое из файла `wwwroot/index.html` клиентского приложения в файл `Pages/_Host.cshtml`. Обновите содержимое файла:

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

Настройте Identity с помощью стороннего поставщика входа. Получите токены, необходимые для доступа к сторонним API, и сохраните их.

При входе пользователя в систему Identity собирает маркеры доступа и обновления в рамках процесса проверки подлинности. На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Использование токена доступа сервера для получения стороннего токена доступа

С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера. Затем воспользуйтесь сторонним маркером доступа для вызова ресурсов стороннего API непосредственно из Identity в клиенте.

Этот вариант использовать не рекомендуется. Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента. С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента. Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.

* Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.
* Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Отправка вызовов API с API клиента на API сервера для вызова сторонних API

Отправьте вызов API с API клиента на API сервера. На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.

Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.

* Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.
* Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.

## <a name="use-open-id-connect-oidc-v20-endpoints"></a>Использование конечных точек Open ID Connect (OIDC) версии 2.0

В библиотеке проверки подлинности и шаблонах Blazor используются конечные точки Open ID Connect (OIDC) версии 1.0. Чтобы использовать конечную точку версии 2.0, настройте параметр <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority?displayProperty=nameWithType> маркера носителя JWT. В следующем примере для AAD настраивается версия 2.0 путем добавления сегмента `v2.0` к свойству <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions.Authority>:

```csharp
builder.Services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, 
    options =>
    {
        options.Authority += "/v2.0";
    });
```

Кроме того, параметр можно задать в файле параметров приложения (`appsettings.json`):

```json
{
  "Local": {
    "Authority": "https://login.microsoftonline.com/common/oauth2/v2.0/",
    ...
  }
}
```

Если переход на сегмент в центре сертификации не подходит для поставщика OIDC приложения, например в случае с поставщиками, не являющимися владельцами AAD, задайте свойство <xref:Microsoft.AspNetCore.Builder.OpenIdConnectOptions.Authority> напрямую. Задайте свойство либо в <xref:Microsoft.AspNetCore.Builder.JwtBearerOptions>, либо в файле параметров приложения (`appsettings.json`) с помощью ключа `Authority`.

Список утверждений в маркере идентификатора отличается для конечных точек версии 2.0. Дополнительные сведения см. в статье [Зачем выполнять обновление до платформы удостоверений Майкрософт (версия 2.0)?](/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison)
