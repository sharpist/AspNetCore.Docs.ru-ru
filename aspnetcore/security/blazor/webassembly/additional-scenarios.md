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
# <a name="aspnet-core-blazor-webassembly-additional-security-scenarios"></a><span data-ttu-id="6b4f8-102">ASP.NET Core Blazor WebAssembly дополнительные сценарии безопасности</span><span class="sxs-lookup"><span data-stu-id="6b4f8-102">ASP.NET Core Blazor WebAssembly additional security scenarios</span></span>

<span data-ttu-id="6b4f8-103">Хавьер [Кальварро Нельсон](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="6b4f8-103">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

## <a name="request-additional-access-tokens"></a><span data-ttu-id="6b4f8-104">Запрос дополнительных токенов доступа</span><span class="sxs-lookup"><span data-stu-id="6b4f8-104">Request additional access tokens</span></span>

<span data-ttu-id="6b4f8-105">Большинству приложений требуется только токен доступа для взаимодействия с используемыми ими ресурсами.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-105">Most apps only require an access token to interact with the protected resources that they use.</span></span> <span data-ttu-id="6b4f8-106">В некоторых сценариях для взаимодействия с двумя или более ресурсами приложению может потребоваться несколько токенов.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-106">In some scenarios, an app might require more than one token in order to interact with two or more resources.</span></span>

<span data-ttu-id="6b4f8-107">В следующем примере приложение требует от приложения дополнительных областей API Active Directory (AAD) для чтения данных пользователей и отправки почты.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-107">In the following example, additional Azure Active Directory (AAD) Microsoft Graph API scopes are required by an app to read user data and send mail.</span></span> <span data-ttu-id="6b4f8-108">После добавления разрешений Microsoft Graph API на портал Azure AAD дополнительные`Program.Main`области настраиваются в приложении Клиента (, *Program.cs):*</span><span class="sxs-lookup"><span data-stu-id="6b4f8-108">After adding the Microsoft Graph API permissions in the Azure AAD portal, the additional scopes are configured in the Client app (`Program.Main`, *Program.cs*):</span></span>

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

<span data-ttu-id="6b4f8-109">Метод `IAccessTokenProvider.RequestToken` обеспечивает перегрузку, которая позволяет приложению предоставить токен с данным набором областей, как видно в следующем примере:</span><span class="sxs-lookup"><span data-stu-id="6b4f8-109">The `IAccessTokenProvider.RequestToken` method provides an overload that allows an app to provision a token with a given set of scopes, as seen in the following example:</span></span>

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

<span data-ttu-id="6b4f8-110">`TryGetToken`Возвращает:</span><span class="sxs-lookup"><span data-stu-id="6b4f8-110">`TryGetToken` returns:</span></span>

* <span data-ttu-id="6b4f8-111">`true`с `token` для использования.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-111">`true` with the `token` for use.</span></span>
* <span data-ttu-id="6b4f8-112">`false`если маркер не извлечен.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-112">`false` if the token isn't retrieved.</span></span>

## <a name="handle-token-request-errors"></a><span data-ttu-id="6b4f8-113">Обработка ошибок запроса токенов</span><span class="sxs-lookup"><span data-stu-id="6b4f8-113">Handle token request errors</span></span>

<span data-ttu-id="6b4f8-114">Когда одностраничное приложение (SPA) проверяет подлинность пользователя с помощью Open ID Connect (OIDC), состояние аутентификации поддерживается локально в SPA и в поставщике идентификации (IP) в виде cookie-сеанса, установленного в результате предоставления пользователем своих учетных данных.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-114">When a Single Page Application (SPA) authenticates a user using Open ID Connect (OIDC), the authentication state is maintained locally within the SPA and in the Identity Provider (IP) in the form of a session cookie that's set as a result of the user providing their credentials.</span></span>

<span data-ttu-id="6b4f8-115">Токены, которые IP испускает для пользователя, как правило, действительны в течение коротких периодов времени, около одного часа обычно, поэтому клиентское приложение должно регулярно получать новые токены.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-115">The tokens that the IP emits for the user typically are valid for short periods of time, about one hour normally, so the client app must regularly fetch new tokens.</span></span> <span data-ttu-id="6b4f8-116">В противном случае пользователь будет зарегистрирован после истечения срока действия предоставленных токенов.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-116">Otherwise, the user would be logged-out after the granted tokens expire.</span></span> <span data-ttu-id="6b4f8-117">В большинстве случаев клиенты OIDC могут предоставлять новые токены, не требуя от пользователя повторной аутентификации благодаря состоянию аутентификации или "сессии", которая хранится в IP.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-117">In most cases, OIDC clients are able to provision new tokens without requiring the user to authenticate again thanks to the authentication state or "session" that is kept within the IP.</span></span>

<span data-ttu-id="6b4f8-118">Есть случаи, когда клиент не может получить токен без взаимодействия с пользователем, например, когда по какой-то причине пользователь явно выходит из IP.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-118">There are some cases in which the client can't get a token without user interaction, for example, when for some reason the user explicitly logs out from the IP.</span></span> <span data-ttu-id="6b4f8-119">Этот сценарий возникает, если пользователь посещает `https://login.microsoftonline.com` и регистрируется. В этих сценариях приложение не сразу знает, что пользователь вышел из системы. Любой маркер, который удерживает клиент, может быть недействительным.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-119">This scenario occurs if a user visits `https://login.microsoftonline.com` and logs out. In these scenarios, the app doesn't know immediately that the user has logged out. Any token that the client holds might no longer be valid.</span></span> <span data-ttu-id="6b4f8-120">Кроме того, клиент не может предоставить новый маркер без взаимодействия с пользователем после истечения текущего токена.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-120">Also, the client isn't able to provision a new token without user interaction after the current token expires.</span></span>

<span data-ttu-id="6b4f8-121">Эти сценарии не специфичны для проверки подлинности на основе токенов.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-121">These scenarios aren't specific to token-based authentication.</span></span> <span data-ttu-id="6b4f8-122">Они являются частью природы СПА.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-122">They are part of the nature of SPAs.</span></span> <span data-ttu-id="6b4f8-123">SPA с помощью файлов cookie также не вызывает API сервера, если файл проверки подлинности удален.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-123">An SPA using cookies also fails to call a server API if the authentication cookie is removed.</span></span>

<span data-ttu-id="6b4f8-124">Когда приложение выполняет вызовы API на защищенные ресурсы, вы должны знать следующее:</span><span class="sxs-lookup"><span data-stu-id="6b4f8-124">When an app performs API calls to protected resources, you must be aware of the following:</span></span>

* <span data-ttu-id="6b4f8-125">Для предоставления нового токена доступа для вызова API пользователю может потребоваться сновааутировать подлинность.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-125">To provision a new access token to call the API, the user might be required to authenticate again.</span></span>
* <span data-ttu-id="6b4f8-126">Даже если у клиента есть маркер, который кажется действительным, вызов на сервер может выйти из строя, поскольку маркер был отозван пользователем.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-126">Even if the client has a token that seems to be valid, the call to the server might fail because the token was revoked by the user.</span></span>

<span data-ttu-id="6b4f8-127">Когда приложение запрашивает токен, есть два возможных результата:</span><span class="sxs-lookup"><span data-stu-id="6b4f8-127">When the app requests a token, there are two possible outcomes:</span></span>

* <span data-ttu-id="6b4f8-128">Запрос удался, и приложение имеет действительный маркер.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-128">The request succeeds, and the app has a valid token.</span></span>
* <span data-ttu-id="6b4f8-129">Запрос не удается, и приложение должно проверить подлинность пользователя снова, чтобы получить новый маркер.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-129">The request fails, and the app must authenticate the user again to obtain a new token.</span></span>

<span data-ttu-id="6b4f8-130">При стечении требования маркера необходимо решить, хотите ли вы сохранить текущее состояние перед выполнением перенаправления.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-130">When a token request fails, you need to decide whether you want to save any current state before you perform a redirection.</span></span> <span data-ttu-id="6b4f8-131">Существует несколько подходов с возрастающими уровнями сложности:</span><span class="sxs-lookup"><span data-stu-id="6b4f8-131">Several approaches exist with increasing levels of complexity:</span></span>

* <span data-ttu-id="6b4f8-132">Храните текущее состояние страницы в хранилище сеансов.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-132">Store the current page state in session storage.</span></span> <span data-ttu-id="6b4f8-133">Во `OnInitializeAsync`время проверки, можно ли восстановить состояние до его продолжения.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-133">During `OnInitializeAsync`, check if state can be restored before continuing.</span></span>
* <span data-ttu-id="6b4f8-134">Добавьте параметр строки запроса и используйте его как способ сигнализировать приложению о необходимости повторного гидратации ранее сохраненного состояния.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-134">Add a query string parameter and use that as a way to signal the app that it needs to re-hydrate the previously saved state.</span></span>
* <span data-ttu-id="6b4f8-135">Добавьте параметр строки запроса с уникальным идентификатором для хранения данных в хранилище сеансов, не рискуя столкновениями с другими элементами.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-135">Add a query string parameter with a unique identifier to store data in session storage without risking collisions with other items.</span></span>

<span data-ttu-id="6b4f8-136">В приведенном ниже примере показано, как выполнить следующие задачи.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-136">The following example shows how to:</span></span>

* <span data-ttu-id="6b4f8-137">Сохранить состояние перед перенаправлением на страницу входа.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-137">Preserve state before redirecting to the login page.</span></span>
* <span data-ttu-id="6b4f8-138">Восстановление предыдущего состояния после проверки подлинности с помощью параметра строки запроса.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-138">Recover the previous state afterward authentication using the query string parameter.</span></span>

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

## <a name="save-app-state-before-an-authentication-operation"></a><span data-ttu-id="6b4f8-139">Сохранить состояние приложения перед операцией проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="6b4f8-139">Save app state before an authentication operation</span></span>

<span data-ttu-id="6b4f8-140">Во время операции проверки подлинности есть случаи, когда требуется сохранить состояние приложения до того, как браузер будет перенаправлен на IP- ис.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-140">During an authentication operation, there are cases where you want to save the app state before the browser is redirected to the IP.</span></span> <span data-ttu-id="6b4f8-141">Это может быть в случае, когда вы используете что-то вроде контейнера состояния, и вы хотите восстановить состояние после успешной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-141">This can be the case when you are using something like a state container and you want to restore the state after the authentication succeeds.</span></span> <span data-ttu-id="6b4f8-142">Можно использовать пользовательский объект состояния аутентификации для сохранения состояния приложения или ссылки на него и восстановления этого состояния после успешного завершения операции проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-142">You can use a custom authentication state object to preserve app-specific state or a reference to it and restore that state once the authentication operation successfully completes.</span></span>

<span data-ttu-id="6b4f8-143">`Authentication`компонент *(Страницы/Аутентификация.бритва):*</span><span class="sxs-lookup"><span data-stu-id="6b4f8-143">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

## <a name="customize-app-routes"></a><span data-ttu-id="6b4f8-144">Настройка маршрутов приложений</span><span class="sxs-lookup"><span data-stu-id="6b4f8-144">Customize app routes</span></span>

<span data-ttu-id="6b4f8-145">По умолчанию `Microsoft.AspNetCore.Components.WebAssembly.Authentication` библиотека использует маршруты, указанные в следующей таблице, для представления различных состояний аутентификации.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-145">By default, the `Microsoft.AspNetCore.Components.WebAssembly.Authentication` library uses the routes shown in the following table for representing different authentication states.</span></span>

| <span data-ttu-id="6b4f8-146">Маршрут</span><span class="sxs-lookup"><span data-stu-id="6b4f8-146">Route</span></span>                            | <span data-ttu-id="6b4f8-147">Назначение</span><span class="sxs-lookup"><span data-stu-id="6b4f8-147">Purpose</span></span> |
| -------------------------------- | ------- |
| `authentication/login`           | <span data-ttu-id="6b4f8-148">Запускает операцию ввха.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-148">Triggers a sign-in operation.</span></span> |
| `authentication/login-callback`  | <span data-ttu-id="6b4f8-149">Обрабатывает результат любой операции ввместь.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-149">Handles the result of any sign-in operation.</span></span> |
| `authentication/login-failed`    | <span data-ttu-id="6b4f8-150">Отображает сообщения об ошибках, когда операция ввода по какой-либо причине не удается.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-150">Displays error messages when the sign-in operation fails for some reason.</span></span> |
| `authentication/logout`          | <span data-ttu-id="6b4f8-151">Запускает операцию по вывески.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-151">Triggers a sign-out operation.</span></span> |
| `authentication/logout-callback` | <span data-ttu-id="6b4f8-152">Обрабатывает результат операции выведения.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-152">Handles the result of a sign-out operation.</span></span> |
| `authentication/logout-failed`   | <span data-ttu-id="6b4f8-153">Отображает сообщения об ошибках, когда операция выхода по каким-то причинам завершается сбоем.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-153">Displays error messages when the sign-out operation fails for some reason.</span></span> |
| `authentication/logged-out`      | <span data-ttu-id="6b4f8-154">Означает, что пользователь успешно вышел из системы.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-154">Indicates that the user has successfully logout.</span></span> |
| `authentication/profile`         | <span data-ttu-id="6b4f8-155">Триггеры операции по отсваге профиля пользователя.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-155">Triggers an operation to edit the user profile.</span></span> |
| `authentication/register`        | <span data-ttu-id="6b4f8-156">Запускает операцию по регистрации нового пользователя.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-156">Triggers an operation to register a new user.</span></span> |

<span data-ttu-id="6b4f8-157">Маршруты, указанные в предыдущей `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`таблице, настраиваются через .</span><span class="sxs-lookup"><span data-stu-id="6b4f8-157">The routes shown in the preceding table are configurable via `RemoteAuthenticationOptions<TProviderOptions>.AuthenticationPaths`.</span></span> <span data-ttu-id="6b4f8-158">При настройке параметров для предоставления пользовательских маршрутов, подтвердите, что приложение имеет маршрут, который обрабатывает каждый путь.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-158">When setting options to provide custom routes, confirm that the app has a route that handles each path.</span></span>

<span data-ttu-id="6b4f8-159">В следующем примере все пути закреплены `/security`под .</span><span class="sxs-lookup"><span data-stu-id="6b4f8-159">In the following example, all the paths are prefixed with `/security`.</span></span>

<span data-ttu-id="6b4f8-160">`Authentication`компонент *(Страницы/Аутентификация.бритва):*</span><span class="sxs-lookup"><span data-stu-id="6b4f8-160">`Authentication` component (*Pages/Authentication.razor*):</span></span>

```razor
@page "/security/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code{
    [Parameter]
    public string Action { get; set; }
}
```

<span data-ttu-id="6b4f8-161">`Program.Main`*(Program.cs):*</span><span class="sxs-lookup"><span data-stu-id="6b4f8-161">`Program.Main` (*Program.cs*):</span></span>

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

<span data-ttu-id="6b4f8-162">Если требование требует совершенно разных путей, установите маршруты, как описано ранее, и свяжете `RemoteAuthenticatorView` с явным параметром действия:</span><span class="sxs-lookup"><span data-stu-id="6b4f8-162">If the requirement calls for completely different paths, set the routes as described previously and render the `RemoteAuthenticatorView` with an explicit action parameter:</span></span>

```razor
@page "/register"

<RemoteAuthenticatorView Action="@RemoteAuthenticationActions.Register" />
```

<span data-ttu-id="6b4f8-163">Вы можете разбить uI на разные страницы, если вы решите сделать это.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-163">You're allowed to break the UI into different pages if you choose to do so.</span></span>

## <a name="customize-the-authentication-user-interface"></a><span data-ttu-id="6b4f8-164">Настройка пользовательского интерфейса проверки подлинности</span><span class="sxs-lookup"><span data-stu-id="6b4f8-164">Customize the authentication user interface</span></span>

<span data-ttu-id="6b4f8-165">`RemoteAuthenticatorView`включает набор элементов uI по умолчанию для каждого состояния проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-165">`RemoteAuthenticatorView` includes a default set of UI pieces for each authentication state.</span></span> <span data-ttu-id="6b4f8-166">Каждое состояние может быть настроено `RenderFragment`путем передачи в пользовательском .</span><span class="sxs-lookup"><span data-stu-id="6b4f8-166">Each state can be customized by passing in a custom `RenderFragment`.</span></span> <span data-ttu-id="6b4f8-167">Для настройки отображаемого текста во время `RemoteAuthenticatorView` первоначального процесса входа можно изменить следующее.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-167">To customize the displayed text during the initial login process, can change the `RemoteAuthenticatorView` as follows.</span></span>

<span data-ttu-id="6b4f8-168">`Authentication`компонент *(Страницы/Аутентификация.бритва):*</span><span class="sxs-lookup"><span data-stu-id="6b4f8-168">`Authentication` component (*Pages/Authentication.razor*):</span></span>

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

<span data-ttu-id="6b4f8-169">Имеет `RemoteAuthenticatorView` один фрагмент, который может быть использован по маршруту проверки подлинности, показанному в следующей таблице.</span><span class="sxs-lookup"><span data-stu-id="6b4f8-169">The `RemoteAuthenticatorView` has one fragment that can be used per authentication route shown in the following table.</span></span>

| <span data-ttu-id="6b4f8-170">Маршрут</span><span class="sxs-lookup"><span data-stu-id="6b4f8-170">Route</span></span>                            | <span data-ttu-id="6b4f8-171">Fragment (Фрагмент)</span><span class="sxs-lookup"><span data-stu-id="6b4f8-171">Fragment</span></span>                |
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
