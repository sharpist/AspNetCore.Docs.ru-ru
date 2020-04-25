<span data-ttu-id="2221d-101">Страница, созданная `Authentication` компонентом (*pages/Authentication. Razor*), определяет маршруты, необходимые для обработки различных стадий проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2221d-101">The page produced by the `Authentication` component (*Pages/Authentication.razor*) defines the routes required for handling different authentication stages.</span></span>

<span data-ttu-id="2221d-102">`RemoteAuthenticatorView` Компонент:</span><span class="sxs-lookup"><span data-stu-id="2221d-102">The `RemoteAuthenticatorView` component:</span></span>

* <span data-ttu-id="2221d-103">Предоставляется пакетом [Microsoft. AspNetCore. Components. веб-сборка. Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) .</span><span class="sxs-lookup"><span data-stu-id="2221d-103">Is provided by the [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span>
* <span data-ttu-id="2221d-104">Управляет выполнением соответствующих действий на каждом этапе проверки подлинности.</span><span class="sxs-lookup"><span data-stu-id="2221d-104">Manages performing the appropriate actions at each stage of authentication.</span></span>

```razor
@page "/authentication/{action}"
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication

<RemoteAuthenticatorView Action="@Action" />

@code {
    [Parameter]
    public string Action { get; set; }
}
```
