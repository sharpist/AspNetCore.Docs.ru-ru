Страница, созданная компонентом `Authentication` (`Pages/Authentication.razor`), определяет маршруты, необходимые для обработки различных этапов проверки подлинности.

Компонент <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>:

* Предоставляется пакетом [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/).
* Управляет выполнением соответствующих действий на каждом этапе проверки подлинности.

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
