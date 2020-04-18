Компонент `FetchData` показывает, как:

* Предоставление токена доступа.
* Используйте маркер доступа для вызова защищенного API ресурса в приложении *Server.*

Директива `@attribute [Authorize]` указывает системе авторизации Blazor WebAssembly, что пользователь должен быть уполномочен для посещения этого компонента. Наличие атрибута в приложении *«Клиент»* не препятствует вызову API на сервере без надлежащих учетных данных. Приложение *Server* также `[Authorize]` должно использоваться на соответствующих конечных точках, чтобы правильно защитить их.

`AuthenticationService.RequestAccessToken();`заботится о запросе маркера доступа, который может быть добавлен в запрос на вызов API. Если токен кэширован или служба может предоставить новый токен доступа без взаимодействия с пользователем, запрос маркера удается. В противном случае запрос маркера не удается.

Для того, чтобы получить фактический токен, чтобы включить в запрос, `tokenResult.TryGetToken(out var token)`приложение должно проверить, что запрос удалось, позвонив . 

Если запрос был успешным, переменная токена заполняется токеном. Свойство `Value` токена предоставляет буквальную строку `Authorization` для включения в заголовок запроса.

Если запрос не удалось, поскольку маркер не может быть подготовлен без взаимодействия с пользователем, результат маркера содержит URL-адрес перенаправления. Навигация по этому URL-адресу переносит пользователя на страницу входа и возвращается на текущую страницу после успешной проверки подлинности.

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
            forecasts = await httpClient.GetJsonAsync<WeatherForecast[]>(
                "WeatherForecast");
        }
        else
        {
            Navigation.NavigateTo(tokenResult.RedirectUrl);
        }

    }
}
```

Для получения дополнительной информации смотрите [состояние приложения «Сохранить» перед операцией проверки подлинности.](xref:security/blazor/webassembly/additional-scenarios#save-app-state-before-an-authentication-operation)
