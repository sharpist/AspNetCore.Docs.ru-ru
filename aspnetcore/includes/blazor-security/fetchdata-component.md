<span data-ttu-id="5d7a3-101">В `FetchData` компоненте показано, как:</span><span class="sxs-lookup"><span data-stu-id="5d7a3-101">The `FetchData` component shows how to:</span></span>

* <span data-ttu-id="5d7a3-102">Подготавливает маркер доступа.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-102">Provision an access token.</span></span>
* <span data-ttu-id="5d7a3-103">Используйте маркер доступа для вызова API защищенных ресурсов в *серверном* приложении.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-103">Use the access token to call a protected resource API in the *Server* app.</span></span>

<span data-ttu-id="5d7a3-104">`@attribute [Authorize]` Директива указывает системе авторизации веб блазор, что пользователь должен быть авторизован для посещения этого компонента.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-104">The `@attribute [Authorize]` directive indicates to the Blazor WebAssembly authorization system that the user must be authorized in order to visit this component.</span></span> <span data-ttu-id="5d7a3-105">Наличие атрибута в *клиентском* приложении не мешает вызову API на сервере без соответствующих учетных данных.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-105">The presence of the attribute in the *Client* app doesn't prevent the API on the server from being called without proper credentials.</span></span> <span data-ttu-id="5d7a3-106">*Серверное* приложение также должно использовать `[Authorize]` на соответствующих конечных точках для правильной защиты.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-106">The *Server* app also must use `[Authorize]` on the appropriate endpoints to correctly protect them.</span></span>

<span data-ttu-id="5d7a3-107">`AuthenticationService.RequestAccessToken();`позаботится о запросе маркера доступа, который можно добавить в запрос для вызова API.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-107">`AuthenticationService.RequestAccessToken();` takes care of requesting an access token that can be added to the request to call the API.</span></span> <span data-ttu-id="5d7a3-108">Если маркер кэшируется или служба может подготавливать новый маркер доступа без вмешательства пользователя, запрос маркера будет выполнен.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-108">If the token is cached or the service is able to provision a new access token without user interaction, the token request succeeds.</span></span> <span data-ttu-id="5d7a3-109">В противном случае запрос маркера завершится ошибкой.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-109">Otherwise, the token request fails.</span></span>

<span data-ttu-id="5d7a3-110">Чтобы получить фактический токен для включения в запрос, приложение должно проверить, что запрос прошел, вызвав метод `tokenResult.TryGetToken(out var token)`.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-110">In order to obtain the actual token to include in the request, the app must check that the request succeeded by calling `tokenResult.TryGetToken(out var token)`.</span></span> 

<span data-ttu-id="5d7a3-111">Если запрос был успешным, переменная маркера заполняется маркером доступа.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-111">If the request was successful, the token variable is populated with the access token.</span></span> <span data-ttu-id="5d7a3-112">`Value` Свойство маркера предоставляет строку литерала для включения в заголовок `Authorization` запроса.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-112">The `Value` property of the token exposes the literal string to include in the `Authorization` request header.</span></span>

<span data-ttu-id="5d7a3-113">Если запрос завершился ошибкой, так как не удалось подготовить маркер без взаимодействия с пользователем, результат маркера содержит URL-адрес перенаправления.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-113">If the request failed because the token couldn't be provisioned without user interaction, the token result contains a redirect URL.</span></span> <span data-ttu-id="5d7a3-114">При переходе по этому URL-адресу пользователь переходит на страницу входа и возвращается к текущей странице после успешной проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="5d7a3-114">Navigating to this URL takes the user to the login page and back to the current page after a successful authentication.</span></span>

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
