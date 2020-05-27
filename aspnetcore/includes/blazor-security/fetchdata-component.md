В `FetchData` компоненте показано, как:

* Подготавливает маркер доступа.
* Используйте маркер доступа для вызова API защищенных ресурсов в *серверном* приложении.

[`@attribute [Authorize]`](xref:mvc/views/razor#attribute)Директива указывает системе авторизации веб блазор, что пользователь должен быть авторизован для посещения этого компонента. Наличие атрибута в *клиентском* приложении не мешает вызову API на сервере без соответствующих учетных данных. *Серверное* приложение также должно использовать `[Authorize]` на соответствующих конечных точках для правильной защиты.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.IAccessTokenProvider.RequestAccessToken%2A?displayProperty=nameWithType>позаботится о запросе маркера доступа, который можно добавить в запрос для вызова API. Если маркер кэшируется или служба может подготавливать новый маркер доступа без вмешательства пользователя, запрос маркера будет выполнен. В противном случае запрос маркера завершается ошибкой <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenNotAvailableException> , который перехватывается инструкцией [try-catch](/dotnet/csharp/language-reference/keywords/try-catch) .

Чтобы получить фактический токен для включения в запрос, приложение должно проверить, что запрос был обработан, вызвав метод [токенресулт. трижеттокен (out ДИСП token)](xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessTokenResult.TryGetToken%2A).

Если запрос был успешным, переменная маркера заполняется маркером доступа. <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.AccessToken.Value?displayProperty=nameWithType>Свойство маркера предоставляет строку литерала для включения в `Authorization` заголовок запроса.

Если запрос завершился ошибкой, так как не удалось подготовить маркер без взаимодействия с пользователем, результат маркера содержит URL-адрес перенаправления. При переходе по этому URL-адресу пользователь переходит на страницу входа и возвращается к текущей странице после успешной проверки подлинности.

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
