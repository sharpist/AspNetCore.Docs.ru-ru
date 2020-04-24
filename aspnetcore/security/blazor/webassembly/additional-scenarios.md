---
title: ASP.NET Core Blazor дополнительных сценариев безопасности для сборки
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 2dbb2bbd07c427c594a12b8037f35cfff2228191
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111179"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Блазор дополнительные сценарии безопасности для сборки

Автор: [Javier Calvarro Nelson](https://github.com/javiercn) (Хавьер Кальварро Нельсон)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> Рекомендации в этой статье применимы к предварительной версии 4 ASP.NET Core 3,2. Этот раздел будет обновлен для ознакомительной версии 5 в пятницу, 24 апреля.

## <a name="request-additional-access-tokens"></a>Запрос дополнительных маркеров доступа

Большинству приложений требуется только маркер доступа для взаимодействия с защищенными ресурсами, которые они используют. В некоторых сценариях для взаимодействия с двумя и более ресурсами приложению может потребоваться несколько маркеров.

В следующем примере дополнительные Azure Active Directory (AAD) Microsoft Graph области API необходимы приложениям для чтения данных пользователей и отправки почты. После добавления разрешений Microsoft Graph API на портале Azure AAD дополнительные области настраиваются в клиентском приложении (`Program.Main` *Program.CS*):

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

`IAccessTokenProvider.RequestToken` Метод предоставляет перегрузку, которая позволяет приложению подготавливать маркер с заданным набором областей, как показано в следующем примере:

```csharp
var tokenResult = await AuthenticationService.RequestAccessToken(
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

`TryGetToken`возврата

* `true`с помощью `token` для использования.
* `false`значение, если маркер не извлекается.

## <a name="attach-tokens-to-outgoing-requests"></a>Присоединение маркеров к исходящим запросам

`AuthorizationMessageHandler` Службу можно использовать с `HttpClient` для присоединения маркеров доступа к исходящим запросам. Токены получаются с помощью `IAccessTokenProvider` существующей службы. Если токен не может быть получен, `AccessTokenNotAvailableException` создается исключение. `AccessTokenNotAvailableException`содержит `Redirect` метод, который можно использовать для перехода пользователя к поставщику удостоверений для получения нового маркера. Для `AuthorizationMessageHandler` настройки можно использовать полномочные URL-адреса, области и URL-адрес возврата с `ConfigureHandler` помощью метода.

В `AuthorizationMessageHandler` следующем примере `HttpClient` настраивается в `Program.Main` (*Program.CS*):

```csharp
builder.Services.AddSingleton(sp =>
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

Для удобства `BaseAddressAuthorizationMessageHandler` включается предварительно настроенный базовый адрес приложения в качестве разрешенного URL-адреса. Шаблоны Блазор `HttpClient` с поддержкой проверки подлинности теперь используют [ихттпклиентфактори](https://docs.microsoft.com/aspnet/core/fundamentals/http-requests) для настройки с помощью: `BaseAddressAuthorizationMessageHandler`

```csharp
builder.Services.AddHttpClient("BlazorWithIdentityApp1.ServerAPI", 
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
        .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>().CreateClient("BlazorWithIdentityApp1.ServerAPI"));
```

При создании клиента с помощью `CreateClient` в предыдущем примере `HttpClient` передаются экземпляры, которые включают маркеры доступа при выполнении запросов к серверному проекту.

Настроенный `HttpClient` затем используется для выполнения запросов с помощью простого `try-catch` шаблона. Следующий `FetchData` компонент запрашивает данные прогноза погоды:

```csharp
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

Кроме того, можно определить типизированный клиент, который обрабатывает все проблемы, связанные с получением маркеров HTTP и Token, в пределах одного класса:

*WeatherClient.CS*:

```csharp
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
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }

        return forecasts;
    }
}
```

*Program.cs*:

```csharp
builder.Services.AddHttpClient<WeatherClient>(
    client => client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();
```

*FetchData. Razor*:

```razor
@inject WeatherClient WeatherClient

...

protected override async Task OnInitializedAsync()
{
    forecasts = await WeatherClient.GetWeatherForeacasts();
}
```

## <a name="handle-token-request-errors"></a>Обработку ошибок запросов маркера

Когда одностраничное приложение (SPA) выполняет проверку подлинности пользователя с помощью Open ID Connect (OIDC), состояние проверки подлинности сохраняется локально в рамках SPA и в поставщике удостоверений (IP) в виде файла cookie сеанса, который задается в результате предоставления учетных данных пользователем.

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

* Сохранение текущего состояния страницы в хранилище сеансов. В `OnInitializeAsync`течение этого периода проверьте, можно ли восстановить состояние перед продолжением.
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

Во время операции аутентификации существуют случаи, когда необходимо сохранить состояние приложения, прежде чем браузер перенаправится на IP-адрес. Это может быть так, если вы используете нечто вроде контейнера состояния и хотите восстановить состояние после завершения проверки подлинности. Можно использовать пользовательский объект состояния проверки подлинности, чтобы сохранить состояние конкретного приложения или ссылку на него, а затем восстановить это состояние после успешного завершения операции проверки подлинности.

`Authentication`Component (*pages/Authentication. Razor*):

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

## <a name="customize-app-routes"></a>Настройка маршрутов приложений

По умолчанию `Microsoft.AspNetCore.Components.WebAssembly.Authentication` библиотека использует маршруты, показанные в следующей таблице, для представления различных состояний проверки подлинности.

| Маршрут                            | Описание |
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

Маршруты, показанные в предыдущей таблице, можно настроить с `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`помощью. При настройке параметров для предоставления настраиваемых маршрутов убедитесь, что у приложения есть маршрут, который обрабатывает каждый путь.

В следующем примере все пути имеют префикс `/security`.

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

`Program.Main`(*Program.CS*):

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

Если требование вызывается для совершенно разных путей, задайте маршруты, как описано выше, и выполните `RemoteAuthenticatorView` визуализацию с помощью явного параметра Action:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

При необходимости вы можете разбить пользовательский интерфейс на разные страницы.

## <a name="customize-the-authentication-user-interface"></a>Настройка пользовательского интерфейса проверки подлинности

`RemoteAuthenticatorView`включает набор элементов пользовательского интерфейса по умолчанию для каждого состояния проверки подлинности. Каждое состояние можно настроить путем передачи пользовательского `RenderFragment`. Для настройки отображаемого текста во время первоначального входа в систему может измениться следующим `RemoteAuthenticatorView` образом.

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

В `RemoteAuthenticatorView` имеется один фрагмент, который можно использовать для каждого маршрута проверки подлинности, как показано в следующей таблице.

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

        builder.Services.AddSingleton(new HttpClient 
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
    services.AddScoped<AuthenticationStateProvider, ServerAuthenticationStateProvider>();
    services.AddScoped<SignOutSessionStateManager>();

    Client.Program.ConfigureCommonServices(services);
}
```

В методе `Startup.Configure` серверного приложения замените `endpoints.MapFallbackToFile("index.html")` на `endpoints.MapFallbackToPage("/_Host")`:

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
          <component type="typeof(Wasm.Authentication.Client.App)" render-mode="Static" />
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

Настройте удостоверение с помощью стороннего поставщика входа. Получите токены, необходимые для доступа к сторонним API, и сохраните их.

При входе пользователя в систему удостоверение собирает токены доступа и обновления в рамках процесса проверки подлинности. На этом этапе существует несколько подходов для отправки вызовов API к сторонним API.

#### <a name="use-a-server-access-token-to-retrieve-the-third-party-access-token"></a>Использование токена доступа сервера для получения стороннего токена доступа

С помощью созданного на сервере токена доступа получите сторонний токен доступа из конечной точки API сервера. Затем воспользуйтесь сторонним токеном доступа для вызова ресурсов стороннего API непосредственно из удостоверения на клиенте.

Этот вариант использовать не рекомендуется. Здесь сторонний токен доступа необходимо рассматривать так, как если бы он был создан для общедоступного клиента. С точки зрения использования OAuth у общедоступного приложения нет секрета клиента, так как оно не может считаться доверенным и надежно хранить секреты, а токен доступа создается для конфиденциального клиента. Конфиденциальный клиент — это клиент, у которого есть секрет клиента. Кроме того, предполагается, что он способен надежно хранить секреты.

* Исходя из того, что третья сторона выдала токен более доверенному клиенту, сторонним токенам доступа могут быть предоставлены дополнительные области для выполнения конфиденциальных операций.
* Аналогичным образом, токены обновления не должны выдаваться недоверенному клиенту, так как в этом случае клиент получает неограниченный доступ до применения других ограничений.

#### <a name="make-api-calls-from-the-client-to-the-server-api-in-order-to-call-third-party-apis"></a>Отправка вызовов API с API клиента на API сервера для вызова сторонних API

Отправьте вызов API с API клиента на API сервера. На сервере получите токен доступа для ресурса стороннего API и осуществите необходимый вызов.

Несмотря на то, что в этом случае для вызова стороннего API требуется выполнить дополнительный сетевой прыжок через сервер, этот подход является более безопасным.

* Сервер может хранить токены обновления и гарантировать, что приложение не потеряет доступ к сторонним ресурсам.
* Приложение не может получать с сервера токены доступа, содержащие более конфиденциальные разрешения.
