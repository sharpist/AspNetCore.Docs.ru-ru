<span data-ttu-id="21d47-101">Компонент `FetchData` показывает, как:</span><span class="sxs-lookup"><span data-stu-id="21d47-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="21d47-102">подготовить маркер доступа;</span><span class="sxs-lookup"><span data-stu-id="21d47-102">Provision an access token.</span></span>
* <span data-ttu-id="21d47-103">использовать маркер доступа для вызова API защищенных ресурсов в приложении *Server*.</span><span class="sxs-lookup"><span data-stu-id="21d47-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="21d47-104">Директива [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) указывает на систему авторизации Blazor WebAssembly, в которой пользователь должен авторизовать для перехода в этот компонент.</span><span class="sxs-lookup"><span data-stu-id="21d47-104">The [`@attribute [Authorize]`](xref:mvc/views/razor#attribute) directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="21d47-105">Наличие атрибута в приложении `Client` не мешает вызову API на сервере без соответствующих учетных данных.</span><span class="sxs-lookup"><span data-stu-id="21d47-105">The presence of the attribute in the `Client` app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="21d47-106">Приложение `Server` также должно использовать `[Authorize]` на соответствующих конечных точках, чтобы правильно защитить их.</span><span class="sxs-lookup"><span data-stu-id="21d47-106">The `Server` app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="21d47-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> запрашивает маркера доступа, который можно добавить в запрос для вызова API.</span><span class="sxs-lookup"><span data-stu-id="21d47-107"><xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType> takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="21d47-108">Если маркер кэшируется или служба может подготовить новый маркер доступа без вмешательства пользователя, запрос маркера будет выполнен.</span><span class="sxs-lookup"><span data-stu-id="21d47-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="21d47-109">В противном случае запрос маркера завершается ошибкой <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, которая перехватывается инструкцией [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch).</span><span class="sxs-lookup"><span data-stu-id="21d47-109">Otherwise, the token request fails with an <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException>, which is caught in a [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statement.</span></span>

<span data-ttu-id="21d47-110">Чтобы получить фактический токен для включения в запрос, приложение должно проверить, что запрос был обработан, вызвав [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span><span class="sxs-lookup"><span data-stu-id="21d47-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling [`tokenResult.TryGetToken(out var token)`](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).</span></span>

<span data-ttu-id="21d47-111">Если запрос был успешным, переменная маркера заполняется маркером доступа.</span><span class="sxs-lookup"><span data-stu-id="21d47-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="21d47-112">Свойство <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> маркера предоставляет строку литерала для включения в заголовок запроса `Authorization`.</span><span class="sxs-lookup"><span data-stu-id="21d47-112">The <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType> property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="21d47-113">Если запрос завершился ошибкой, поскольку не удалось подготовить маркер без взаимодействия с пользователем, результирующий маркер содержит URL-адрес перенаправления.</span><span class="sxs-lookup"><span data-stu-id="21d47-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="21d47-114">При переходе по этому URL-адресу пользователь переходит на страницу входа и возвращается к текущей странице после успешной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="21d47-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

```razor
@page "/fetchdata"
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using {APP NAMESPACE}.Shared
@attribute [Authorize]
@inject HttpClient Http

...

@code {
    private WeatherForecast[] forecasts;

    protected override async Task OnInitializedAsync()
    {
        try
        {
            forecasts = await Http.GetFromJsonAsync<WeatherForecast[]>("WeatherForecast");
        }
        catch (AccessTokenNotAvailableException exception)
        {
            exception.Redirect();
        }
    }
}
```
