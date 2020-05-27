Страница, созданная `Authentication` компонентом (*pages/Authentication. Razor*), определяет маршруты, необходимые для обработки различных стадий проверки подлинности.

<xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.RemoteAuthenticatorView>Компонент:

* Предоставляется пакетом [Microsoft. AspNetCore. Components. веб-сборка. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .
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
