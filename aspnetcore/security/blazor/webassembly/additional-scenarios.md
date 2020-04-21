---
title: ASP.NET Blazor основных веб-сборки дополнительные сценарии безопасности
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/19/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/additional-scenarios
ms.openlocfilehash: 314a7b54ab87295b8ca814f5e369942ae911407e
ms.sourcegitcommit: 5547d920f322e5a823575c031529e4755ab119de
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661596"
---
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a>ASP.NET Core Blazor WebAssembly дополнительные сценарии безопасности

Хавьер [Кальварро Нельсон](https://github.com/javiercn)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a>Запрос дополнительных токенов доступа

Большинству приложений требуется только токен доступа для взаимодействия с используемыми ими ресурсами. В некоторых сценариях для взаимодействия с двумя или более ресурсами приложению может потребоваться несколько токенов.

В следующем примере приложение требует от приложения дополнительных областей API Active Directory (AAD) для чтения данных пользователей и отправки почты. После добавления разрешений Microsoft Graph API на портал Azure AAD дополнительные`Program.Main`области настраиваются в приложении Клиента (, *Program.cs):*

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

Метод `IAccessTokenProvider.RequestToken` обеспечивает перегрузку, которая позволяет приложению предоставить токен с данным набором областей, как видно в следующем примере:

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

`TryGetToken`Возвращает:

* `true`с `token` для использования.
* `false`если маркер не извлечен.

## <a name="handle-token-request-errors"></a>Обработка ошибок запроса токенов

Когда одностраничное приложение (SPA) проверяет подлинность пользователя с помощью Open ID Connect (OIDC), состояние аутентификации поддерживается локально в SPA и в поставщике идентификации (IP) в виде cookie-сеанса, установленного в результате предоставления пользователем своих учетных данных.

Токены, которые IP испускает для пользователя, как правило, действительны в течение коротких периодов времени, около одного часа обычно, поэтому клиентское приложение должно регулярно получать новые токены. В противном случае пользователь будет зарегистрирован после истечения срока действия предоставленных токенов. В большинстве случаев клиенты OIDC могут предоставлять новые токены, не требуя от пользователя повторной аутентификации благодаря состоянию аутентификации или "сессии", которая хранится в IP.

Есть случаи, когда клиент не может получить токен без взаимодействия с пользователем, например, когда по какой-то причине пользователь явно выходит из IP. Этот сценарий возникает, если пользователь посещает `https://login.microsoftonline.com` и регистрируется. В этих сценариях приложение не сразу знает, что пользователь вышел из системы. Любой маркер, который удерживает клиент, может быть недействительным. Кроме того, клиент не может предоставить новый маркер без взаимодействия с пользователем после истечения текущего токена.

Эти сценарии не специфичны для проверки подлинности на основе токенов. Они являются частью природы СПА. SPA с помощью файлов cookie также не вызывает API сервера, если файл проверки подлинности удален.

Когда приложение выполняет вызовы API на защищенные ресурсы, вы должны знать следующее:

* Для предоставления нового токена доступа для вызова API пользователю может потребоваться сновааутировать подлинность.
* Даже если у клиента есть маркер, который кажется действительным, вызов на сервер может выйти из строя, поскольку маркер был отозван пользователем.

Когда приложение запрашивает токен, есть два возможных результата:

* Запрос удался, и приложение имеет действительный маркер.
* Запрос не удается, и приложение должно проверить подлинность пользователя снова, чтобы получить новый маркер.

При стечении требования маркера необходимо решить, хотите ли вы сохранить текущее состояние перед выполнением перенаправления. Существует несколько подходов с возрастающими уровнями сложности:

* Храните текущее состояние страницы в хранилище сеансов. Во `OnInitializeAsync`время проверки, можно ли восстановить состояние до его продолжения.
* Добавьте параметр строки запроса и используйте его как способ сигнализировать приложению о необходимости повторного гидратации ранее сохраненного состояния.
* Добавьте параметр строки запроса с уникальным идентификатором для хранения данных в хранилище сеансов, не рискуя столкновениями с другими элементами.

В приведенном ниже примере показано, как выполнить следующие задачи.

* Сохранить состояние перед перенаправлением на страницу входа.
* Восстановление предыдущего состояния после проверки подлинности с помощью параметра строки запроса.

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

Во время операции проверки подлинности есть случаи, когда требуется сохранить состояние приложения до того, как браузер будет перенаправлен на IP- ис. Это может быть в случае, когда вы используете что-то вроде контейнера состояния, и вы хотите восстановить состояние после успешной проверки подлинности. Можно использовать пользовательский объект состояния аутентификации для сохранения состояния приложения или ссылки на него и восстановления этого состояния после успешного завершения операции проверки подлинности.

`Authentication`компонент *(Страницы/Аутентификация.бритва):*

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

По умолчанию `Microsoft.AspNetCore.Components.WebAssembly.Authentication` библиотека использует маршруты, указанные в следующей таблице, для представления различных состояний аутентификации.

| Маршрут                            | Назначение |
| -------------------------------- | ------- |
| `authentication/login`           | Запускает операцию ввха. |
| `authentication/login-callback`  | Обрабатывает результат любой операции ввместь. |
| `authentication/login-failed`    | Отображает сообщения об ошибках, когда операция ввода по какой-либо причине не удается. |
| `authentication/logout`          | Запускает операцию по вывески. |
| `authentication/logout-callback` | Обрабатывает результат операции выведения. |
| `authentication/logout-failed`   | Отображает сообщения об ошибках, когда операция выхода по каким-то причинам завершается сбоем. |
| `authentication/logged-out`      | Означает, что пользователь успешно вышел из системы. |
| `authentication/profile`         | Триггеры операции по отсваге профиля пользователя. |
| `authentication/register`        | Запускает операцию по регистрации нового пользователя. |

Маршруты, указанные в предыдущей `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`таблице, настраиваются через . При настройке параметров для предоставления пользовательских маршрутов, подтвердите, что приложение имеет маршрут, который обрабатывает каждый путь.

В следующем примере все пути закреплены `/security`под .

`Authentication`компонент *(Страницы/Аутентификация.бритва):*

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

`Program.Main`*(Program.cs):*

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

Если требование требует совершенно разных путей, установите маршруты, как описано ранее, и свяжете `RemoteAuthenticatorView` с явным параметром действия:

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

Вы можете разбить uI на разные страницы, если вы решите сделать это.

## <a name="customize-the-authentication-user-interface"></a>Настройка пользовательского интерфейса проверки подлинности

`RemoteAuthenticatorView`включает набор элементов uI по умолчанию для каждого состояния проверки подлинности. Каждое состояние может быть настроено `RenderFragment`путем передачи в пользовательском . Для настройки отображаемого текста во время `RemoteAuthenticatorView` первоначального процесса входа можно изменить следующее.

`Authentication`компонент *(Страницы/Аутентификация.бритва):*

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

Имеет `RemoteAuthenticatorView` один фрагмент, который может быть использован по маршруту проверки подлинности, показанному в следующей таблице.

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
