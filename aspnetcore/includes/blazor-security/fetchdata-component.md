<span data-ttu-id="428c2-101">Компонент `FetchData` показывает, как:</span><span class="sxs-lookup"><span data-stu-id="428c2-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="428c2-102">Предоставление токена доступа.</span><span class="sxs-lookup"><span data-stu-id="428c2-102">Provision an access token.</span></span>
* <span data-ttu-id="428c2-103">Используйте маркер доступа для вызова защищенного API ресурса в приложении *Server.*</span><span class="sxs-lookup"><span data-stu-id="428c2-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="428c2-104">Директива `@attribute [Authorize]` указывает системе авторизации Blazor WebAssembly, что пользователь должен быть уполномочен для посещения этого компонента.</span><span class="sxs-lookup"><span data-stu-id="428c2-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="428c2-105">Наличие атрибута в приложении *«Клиент»* не препятствует вызову API на сервере без надлежащих учетных данных.</span><span class="sxs-lookup"><span data-stu-id="428c2-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="428c2-106">Приложение *Server* также `[Authorize]` должно использоваться на соответствующих конечных точках, чтобы правильно защитить их.</span><span class="sxs-lookup"><span data-stu-id="428c2-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="428c2-107">`AuthenticationService.RequestAccessToken();`заботится о запросе маркера доступа, который может быть добавлен в запрос на вызов API.</span><span class="sxs-lookup"><span data-stu-id="428c2-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="428c2-108">Если токен кэширован или служба может предоставить новый токен доступа без взаимодействия с пользователем, запрос маркера удается.</span><span class="sxs-lookup"><span data-stu-id="428c2-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="428c2-109">В противном случае запрос маркера не удается.</span><span class="sxs-lookup"><span data-stu-id="428c2-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="428c2-110">Для того, чтобы получить фактический токен, чтобы включить в запрос, `tokenResult.TryGetToken(out var token)`приложение должно проверить, что запрос удалось, позвонив .</span><span class="sxs-lookup"><span data-stu-id="428c2-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="428c2-111">Если запрос был успешным, переменная токена заполняется токеном.</span><span class="sxs-lookup"><span data-stu-id="428c2-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="428c2-112">Свойство `Value` токена предоставляет буквальную строку `Authorization` для включения в заголовок запроса.</span><span class="sxs-lookup"><span data-stu-id="428c2-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="428c2-113">Если запрос не удалось, поскольку маркер не может быть подготовлен без взаимодействия с пользователем, результат маркера содержит URL-адрес перенаправления.</span><span class="sxs-lookup"><span data-stu-id="428c2-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="428c2-114">Навигация по этому URL-адресу переносит пользователя на страницу входа и возвращается на текущую страницу после успешной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="428c2-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject IAccessTokenProvider AuthenticationService
@inject NavigationManager Navigation
@using {APPLICATION NAMESPACE}.Shared
@attribute [Authorize]

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        var httpClient = new HttpClient();
        httpClient.BaseAddress = new Uri(Navigation.BaseUri);

        var tokenResult = await AuthenticationService.RequestAccessToken();

        if (tokenResult.TryGetToken(out var token))
        {
            httpClient.DefaultRequestHeaders.Add("Authorization", 
                $"Bearer {token.Value}");
            forecasts = await httpClient.GetFromJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

<span data-ttu-id="428c2-115">Для получения дополнительной информации смотрите [состояние приложения «Сохранить» перед операцией проверки подлинности.](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)</span><span class="sxs-lookup"><span data-stu-id="428c2-115">For more information, see [Save app state before an authentication operation](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation).</span></span>
