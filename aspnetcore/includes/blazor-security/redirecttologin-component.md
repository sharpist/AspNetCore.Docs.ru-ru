Компонент `RedirectToLogin` (`Shared/RedirectToLogin.razor`):

* управляет перенаправлением неавторизованных пользователей на страницу входа.
* Сохраняет текущий URL-адрес, к которому пользователь пытается получить доступ, чтобы его можно было вернуть на эту страницу, если проверка подлинности прошла успешно.

```razor
@inject NavigationManager Navigation
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(
            $"authentication/login?returnUrl={Uri.EscapeDataString(Navigation.Uri)}");
    }
}
```
